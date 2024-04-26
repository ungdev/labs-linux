# 7.1 - Logging

Les programmes en ligne de commande produisent tout un tas de messages plus ou moins utiles, que l'on ignore bien souvent mais qui peuvent s'avérer indispensables pour le debugging ou la traçabilité.

Le problème, c'est que les *daemons* tournent par définition en tâche de fond, donc leurs journaux ne sont pas directement affichés dans votre terminal. Certains écrivent dans des fichiers rien que pour eux spécifiés dans leur configuration, d'autres envoient leurs journaux à des services spécialisés...

Nous allons donc nous intéresser à ces fameux *logs*, qui nous paraissent encombrants jusqu'au moment où on a besoin d'eux.

+ [7.1.1 - Syslog](logging.md#711-syslog)
+ [7.1.2 - Inspection des logs](logging.md#712-inspection-des-logs)
+ [7.1.3 - Rotation des logs](logging.md#713-rotation-des-logs)
+ [7.1.4 - Pour aller plus loin - Aggrégation et filtrage des logs](logging.md#714-pour-aller-plus-loin---aggrégation-et-filtrage-des-logs)


## 7.1.1 - Syslog
*Syslog* est un service et un protocole réseau de courtage de *logs*. 

Il est incontournable en admin sys et réseau.

### Principe

+ Les applications qui tournent sur ton OS envoient leurs messages **à un démon Syslog local**, qui sait ensuite quoi faire avec ces messages :
    - Les stocker dans des fichiers, localement ...
    - Les ignorer ...
    - Les transférer à un autre serveur Syslog ...
+ Sur une infrastructure avec plusieurs serveurs, on choisit généralement de rediriger les logs vers un serveur central dédié au *logging*, tout en gardant, éventuellement, une copie locale des logs directement sur le serveur.
    - Cette architecture centralisée permet :
        * D'avoir accès aux **journaux de tous les hôtes en un même endroit** - pratique pour le débugging et l'aggrégation de logs lorsque plusieurs services dépendent les uns des autres
        * De mettre en place des **politiques de stockage et de sauvegarde très poussées pour les logs**
        * D'appliquer une **même config Syslog simpliste** à tous les autres hôtes de l'infra : "remonte tous tes logs au serveur central" - et d'avoir une **configuration complexe à maintenir uniquement au niveau du serveur central**.
    - Garde à l'esprit qu'un serveur de *logging* central peut avoir des **besoins colossaux** en termes **d'espace de stockage** et de **bande passante**.

#### Severity et Facility
*Syslog* classe les événements par gravité (**_severity_**) et source (**_facility_**, en quelque sorte la "partie du système" .concernée par le message).

Cela permet ensuite au service de **sélectionner tels messages** pour leur **appliquer telle action**. Par exemple ...

+ En distinguant par *facility*, on pourrait **écrire séparément, dans des fichier spécifiques**, tous les messages concernant les services mail, et tous les messages concernant des connexions d'utilisateurs au système.
+ En distinguant par *severity*, on pourrait être encore plus granulaires, en rangeant dans un fichier les messages d'erreur concernant les mail, et dans un autre tous les messages informationnels.


+ Il y a **8 niveaux de _severity_**. Classés par ordre de gravité décroissante, cela donne :
    - 0 : *emergency* - urgence, système inutilisable
    - 1 : *alert* - alerte, une action doit être prise immédiatement
    - 2 : *crit* - critique
    - 3 : *err* - erreur 
        * Le type de message auquel vous vous intéresserez le plus souvent
        * (Typiquement, une erreur qui empêche un service de démarrer)
    - 4 : *warn* - avertissement
    - 5 : *notice* - événement normal mais significatif
    - 6 : *info* - informationnel
    - 7 : *debug* - débogage. 
        * En général, cela correspond aux messages émis uniquement quand le programme est lancé ponctuellement dans le mode le plus "verbose" possible, à des fins de débugging (comprendre ce qu'il se passe quand un utilisateur se connecte ...).
        * Il faut éviter de logger trop d'informations inutiles - cela occupe beaucoup d'espace disque sur les serveurs de logs ainsi que de la bande passante.
+ Quelques exemples de *facilities* :
    - 0 : *kern* - messages du kernel
    - 2 : *mail*
    - 3 : *daemon* - facility utilisée par défaut par de nombreux *daemons* qui n'ont pas leur facility dédiée (*freeradius* par exemple)
    - 13 : *security* - messages de sécurité
    - 16-23 : *local0*-*local7* - réservées à un usage personnalisé
    - [Liste des facilities :](https://techdocs.broadcom.com/us/en/symantec-security-software/web-and-network-security/security-analytics/8-2-1/_reference_home/syslog.html)

### Aspects réseau
+ **Port** et protocole de **transport**
    - Historiquement, Syslog utilisait *514/udp*
        * Livraison en mode non-fiable. Des messages peuvent être perdus
        * Plus performant
        * Peut provoquer de la congestion sur le réseau
    - Aujourd'hui, on préfère utiliser __*514/tcp*__
        * Livraison fiable
        * Délais plus importants
        * Contrôle de congestion
+ Les flux de logs sont un **flux sensible**.
    - Les messages de logs peuvent révéler des informations sensibles sur votre SI ou des informations personnelles appartenant à vos utilisateurs.
    - Ils devraient transiter sur le **réseau d'administration** (celui que vous utilisez pour SSH) et non par le réseau de production.
    - On peut éventuellement sécuriser le trafic Syslog en utilisant **TLS**.
+ Un logging excessif peut nuire au performances du réseau. A surveiller !

### Configuration (rsyslog)
<details><code>rsyslog</code> (<i>Rocket-fast Syslog</i>) est l'implémentation de <i>daemon</i> <i>Syslog</i> la plus populaire.

Une alternative est `syslog-ng` - mais préférez `rsyslog`.
</details>

<details>Nous allons voir, à travers un exemple, comment configurer un <i>"client"</i> Syslog, qui ne fera que remonter des logs, et un <i>"serveur central"</i> <i>Syslog</i>, qui leur appliquera des <b>actions</b>.

+ Pour cet exemple, je supposerai que tu disposes de **deux systèmes** (pourquoi pas deux VMs) capables de **communiquer entre eux**.
+ Tu auras besoin du paquet `rsyslog` 
+ Les fichiers de config d'`rsyslog` sont `/etc/rsyslog.conf` et `/etc/rsyslog.d/*.conf`
+ Après toute modification des fichiers de config, redémarre le service `rsyslog` pour qu'elles soient prises en compte.
    - Pense à vérifier le statut du service avec `systemctl` et ses journaux avec `journalctl` en cas de problème.

#### Comprendre la syntaxe des règles rsyslog
<details>

+ La syntaxe de base d'une règle est **`<facility>.<severity> <action>`**.
    - NB : Les niveaux de sévérité sont **hiérarchiquement inclusifs**. En effet, `.<severity>` va sélectionner les logs A PARTIR d'une sévérité *severity*, c'est à dire avec le niveau *severity* OU PIRE.
      * Par exemple, `*.err` (3) sélectionnera aussi les messages de severity `crit` (2), `alert` (1) et `emerg` (0). 
      * Pour sélectionner EXACTEMENT un niveau de sécurity, il faut utiliser l'opérateur `.=` : `<facility>.=<severity> <action>`
    - `mail.err -/var/log/mail.err`
      * Envoie tous les logs de la facility mail, ayant la sévérité `err` ou pire, dans le fichier `/var/log/mail.err`
      * NB : précéder un chemin de fichier par le caractère `-` rend l'**écriture asynchrone.**
        * Le démon bufferise les logs en mémoire pour batcher leur écriture, c'est-à-dire qu'il n'écrit plus directement le message dès qu'il le reçoit, mais attend un court instant au cas où il recevrait d'autres messages. Ainsi, il peut écrire plusieurs messages en une seule opération d'écriture sur disque.
        * **Avantage** : Meilleure durée de vie pour les disques, surtout les SSD dont les performances se dégradent au fil des écritures.
        * **Inconvénient** : Les logs sont seulement en mémoire vive pendant un court instant. Si jamais un incident causait le crash du service, du système ou l'extinction de la machine, les logs seraient perdus à jamais.
    - `security,authpriv.* -/var/log/security`
        * Tous les logs des *facilities* `security` et `authpriv`, quelle que soit leur sévérité, écrits dans le fichier `/var/log/security` de manière asynchrone.
    - `*.err @@syslog.lab.local:514`
        * Envoie tous les logs de sévérité *err* ou pire au serveur Syslog `syslog.lab.local` sur le port 514/**tcp**.
        * Pour du UDP, on mettrait une seule arobase `@`.
+ On peut combiner plusieurs sélections en une seule règle avec l'opérateur `;` :
    - `kern.err;mail.info;daemon,ftp.warn @@syslog.lab.local:514`
+ On peut aussi utiliser le niveau de sévérité `none` pour déselectionner des logs sélectionnés par les expressions précédentes, pour exprimer un "SAUF" :
    - `*.*;auth,authpriv.none -/var/log/messages`
        * Inscrit tous les logs, SAUF ceux des facilities `auth` et `authpriv`, dans `/var/log/messages` de façon asynchrone.
+ Enfin, des *property-based filters* offrent d'autres possibilités de filtrage des logs :
    - `:msg, contains, "certificate" <action>` : *exact match* sur le contenu du message
    - `:msg, !contains, "SUCCESS" <action>` : négation avec l'opérateur `!`
    - `:msg, eregex, "gid=2[0-9][0-9]" <action>` : *Extended regex match* sur le contenu du message
    - `:fromhost-ip, startswith, "172.16." <action>` : *Match* sur les deux premiers octets de l'@IP source du message
    - Pour voir toutes les propriétés disponibles : `man rsyslog.conf | grep -A100 'PROPERTY REPLACER'`

</details>


#### "Client" rsyslog
<details>

Modifier `/etc/rsyslog.conf` :

+ Les modules `imuxsock` et `imklog` doivent être activés :
    - ```s
            module(load="imuxsock")     # recueillir les logs locaux arrivant sur le socket /dev/log
            module(load="imklog")       # recueillir les logs du kernel arrivant sur le buffer de messages du noyau /dev/kmsg
        ```
    - Si ces lignes sont présentes dans la config par défaut, mais commentées avec un '#', vous devez les décommenter.
+ Trouver la section *"Rules"* du fichier. Définir une règle qui remonte tous les messages au serveur de logs central :
    - ```s
            ###############
            #### RULES ####
            ###############
            *.* @@syslog.lab.local:514  # "Quelle que soit la facility, quelle que soit la severity, envoyer le message au serveur Syslog syslog.lab.local:514 (TCP)"
            # Remplacez par l'IP ou le FQDN de votre serveur de logs central

                # @@ : transport TCP
                # ç'aurait été juste @ si le serveur central utilisait UDP
        ```

</details>

#### "Serveur central" rsyslog
<details>

Créer un dossier `/var/log/CENTRAL` (root:root, 0750). Sous ce répertoire, nous allons stocker les logs de chaque serveur surveillé dans un dossier portant son nom, grâce à une règle Syslog.

Créer le fichier `/etc/rsyslog.d/remote.conf`. Y inscrire le contenu suivant :

+ ```s
        $ModLoad imtcp.so 
        #charger le module : Input Module TCP
        # (recueillir des logs Syslog arrivant sur une socket TCP)

        $InputTCPServerRun 514 # Lancer le serveur TCP, en écoutant sur le port 514
    ```
+ ```s
        $template RemoteLogs,"/var/log/CENTRAL/%HOSTNAME%/%$NOW%-%syslogseverity-text%.log"
        # Définir un modèle de chemin "RemoteLogs", de la forme :
            # /var/log/CENTRAL/<nom-du-serveur-surveillé>/<yyyy-mm-dd>-<severity>.log
    ```
+ ```s
        ###############
        #### RULES ####
        ###############
        :FROMHOST-IP, startswith, "192.168.1." -?RemoteLogs 
        & stop # Remplacer "192.168.1." par les octets caractéristiques du subnet des serveurs à surveiller 
    ``` 
    - `:FROMHOST-IP, startswith, <valeur>` : sélectionne les logs sur le critère de leur IP source - quelle que soit leur *facility* et *severity*. 
    - [Il existe de nombreux autres *property-based filters*.](https://www.rsyslog.com/doc/configuration/filters.html)
    - `?RemoteLogs` : applique l'action *"écrire dans le fichier dont le chemin est obtenu en évaluant le template RemoteLogs"*
    - Préfixer un fichier avec `-` : permet d'**écrire de manière asynchrone**, c'est-à-dire, **ne pas écrire directement quand on reçoit le message** mais attendre un peu au cas où il y en ait d'autres pour pouvoir **en écrire plusieurs en une seule opération de disque**. C'est capital pour allonger la durée de vie des disques durs !
    - `& stop` : indiquer qu'on en a terminé avec les logs sélectionnés, pour éviter qu'ils ne soient traités par d'autres règles (en l'occurence, les règles de logging locales définies dans `/etc/rsyslog.conf`).
+ N'oubliez pas d'**ouvrir le port 514/tcp** sur le pare-feu de l'hôte qui joue le rôle de serveur central.

</details>


#### Vérifier la configuration

<details>

+ Emettre un message bidon côté "client" : `logger "Quiche aux poireaux"`
    - La commande `logger` sert à envoyer un message au démon Syslog local - pratique pour tester vos règles.
    - La *severity* par défaut est *notice* (5). Vous pouvez spécifier une *priority* de votre choix : `logger -p err "Pizza à l'ananas"`
+ Côté "serveur central", vous devez pouvoir trouver le fichier `/var/log/CENTRAL/<hostname-client>/$(date +%Y-%m-%d)-notice.log`
+ Il doit contenir le message de test que vous avez envoyé.
    - ![](img/syslog-central.png)

</details>

**NB :** Cette configuration n'était qu'un exemple simpliste. Dans une vraie configuration, on pourrait avoir des fichiers distincts pour chaque *facility*, grouper plusieurs niveaux de sévérité ensemble, envoyer les logs de sécurité à un serveur différent, ignorer certaines classes logs ...

</details>


## 7.1.2 - Rotation des logs
