# 2.3 - Configuration réseau de base

## 2.3.A - Principe
<details><summary>Pour communiquer avec le monde extérieur, votre système a besoin...</summary>

+ D'une **adresse IP**
    - Identifie la machine de manière unique sur le réseau
+ D'un **masque de sous-réseau**
    - Associé à l'adresse IP, permet à la machine de savoir quand elle s'adresse à son réseau local ou à d'autres réseaux
    - *Par exemple, prenons le subnet `192.168.1.0/24` (adresses de `192.168.1.0` à `192.168.1.255`). `192.168.1.10` sait qu'il peut directement contacter `192.168.1.20`, car il est dans le même subnet. En revanche, s'il doit contacter `192.168.2.20`, qui n'est pas dans son subnet, il confiera le paquet à sa passerelle par défaut.*
+ D'une **route par défaut**
    - Adresse IP du routeur qui permet d'atteindre d'autres réseaux.
+ Pour pouvoir résoudre des noms de domaine, de l'adresse d'au moins 1 **serveur DNS**.
    - Allez-vous habituellement voir votre emploi du temps sur `ent.utt.fr`, ou bien sur `193.50.230.1` ? On s'est compris.
    - 
</details>

### Configuration statique ou dynamique
<details>

+ Si vous n'avez pas à configurer ces paramètres à chaque fois que vous vous connectez sur un nouveau réseau avec votre PC, c'est grâce au **DHCP** (*Dynamic Host Configuration Protocol*, parfois aussi connu à tort sous l'appellation *Dark-green Hot Chili Peppers*). Lorsque votre PC rejoint un nouveau réseau, il contacte automatiquement son serveur DHCP pour obtenir tous ces paramètres : on parle alors **d'adressage dynamique**.
    - *Chez vous, le serveur DHCP tourne généralement sur votre box.*
+ En revanche, **pour des serveurs, on préfère l'adressage statique**. On fournit donc tous ces paramètres manuellement.
    - Votre serveur ne va à priori pas se déplacer sur d'autres réseaux
    - On aime bien choisir nous-mêmes son adresse IP, et être sûrs qu'elle ne changera pas.
    - On ne veut pas dépendre d'un autre composant - s'il lui arrivait un malheur, on toute l'infra se retrouverait sans accès au réseau.

</details>

## 2.3.B - Config manuelle temporaire avec iproute2
<details><summary>La commande <code>ip</code> vous permet d'inspecter et modifier manuellement votre config réseau. Les modifications sont <b>temporaires</b>.</summary>

+ `ip address` : affiche les paramètres de niveau 2 et 3 pour toutes vos interfaces
    - `ip a` : raccourci
    - Une interface filaire **ethernet** commence par un `e`, par exemple :
        * `eth0`
        * `ens3`
        * `enp37s0`
    - `ip a show dev <iface>` : montrer les paramètres pour une interface donnée
    - `ip link` pour ne montrer que les paramètres de couche 2
+ `sudo ip address add <address>/<mask> dev <iface>` : ajouter une adresse IPv4 à une interface
    - `sudo ip address del <address>/<mask> dev <iface>` : supprimer une adresse IPv4
    - Exemple : 
        * ```lua
            sudo ip a add 192.168.1.10/24 dev enp37s0
            sudo ip a del 192.168.1.10/24 dev enp37s0
            sudo ip -6 a add 2001:db8:cafe::/64 dev enp37s0     # ipv6 (-6)
            ```
        * Vous pouvez aussi indiquer le masque au format décimal pointé : `sudo ip address add 192.168.1.10 255.255.255.0 dev enp37s0`
    - *NB :*
      - *Une même interface peut avoir plusieurs adresses IP*
      - *Une même machine peut avoir plusieurs interfaces (et donc aussi évidemment avoir plusieurs adresses IP)*
        - *Une même machine peut avoir des IP dans des réseaux différents (pense à un routeur)*
+ `ip route` : affiche la table de routage
+ `ip route add <ip>/<mask> via <ip>` : Ajouter une route
    - Exemple :
        ```lua
            sudo ip route add 172.16.1.0/24 via 192.168.1.254
        ```
    - Remplacer `add` par `del` pour supprimer la route
    - `ip -6` pour de l'IPv6
+ `ip route add default via <ip>` : Ajouter une route par défaut (0.0.0.0/0)

</details>

### Pour aller plus loin
<details>

+ La commande `ip` peut faire bien plus que set votre addresse IP :
    - Créer un [bond LACP](http://www.uni-koeln.de/~pbogusze/posts/LACP_configuration_using_iproute2.html) pour doubler un lien,
    - Changer les paramètres des protocoles bas niveau comme la [MTU](https://www.baeldung.com/linux/maximum-transmission-unit-change-size), 
    - [Configurer des VLANs](https://iamsto.wordpress.com/2018/02/20/howto-linux-iproute2-vlan-configuration-a-k-a-using-ip-command-for-managing-vlans-on-linux/)
    - [Ponter deux interfaces](https://unix.stackexchange.com/questions/255484/how-can-i-bridge-two-interfaces-with-ip-iproute2)  ...
+ `ip route` est capable de gérer routes complexes, voire de gérer plusieurs tables de routage - *par exemple, quand vous avez une interface publique et une interface d'admin, vous pouvez configurer une autre passerelle par défaut pour les paquets sourcés par votre interface d'admin.*

</details>

## 2.3.C - Config persistente avec NetworkManager
<details><summary><i>NetworkManager</i> est un <i>daemon</i> de configuration réseau qui est en train de devenir la méthode de configuration préférée de la plupart des distributions.</summary>

Il définit et gère des profils de configuration qui s'appellent des **connexions**.

On interagit avec ce *daemon* via un front-end comme :
+ [`nmtui`](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide/sec-configuring_ip_networking_with_nmtui) : interface interactive en mode texte
    - Très facile d'utilisation et utilisable sans interface graphique
+ `nmcli` : outil en lignes de commandes
    - Le plus complexe, mais aussi le plus complet et le seul qui puisse être utilisé dans un script.
+ `nm-connection-editor` : interface graphique, beurk
+ Rien ne vous empêche d'aller modifier directement les fichiers de config des connexions à `/etc/NetworkManager/system-connections/*.nmconnection` !

**Nous vous recommandons donc `nmtui`** pour commencer. Il n'y a rien à expliquer tellement c'est ez. Seule chose contre-intuitive, il faut **désactiver puis réactiver** la connexion pour que les modifications soient appliquées.

<details><summary>Quelques commandes <code>nmcli</code></summary>

- `nmcli` : Lister les connexions actives et leurs paramètres essentiels
- `nmcli general` (nmcli g) : Etat général de connectivité du système.
- `nmcli connection` (`nmcli c`) : Lister les connexions
- `nmcli device` (`nmcli d`) : Lister les interfaces
- `nmcli connection show <conn>` (`nmcli c s <conn>`) : Montrer le détail des paramètres d'une connexion
    * `nmcli -f <sections...> connection show <conn>` : Filtrer par catégorie de paramètres - par ex `nmcli -f general,ipv4 c s <conn>`

- `sudo nmcli connection <up|down|reload> <conn>` (`nmcli c <u|d|r> <conn>`) : activer, désactiver ou redémarrer une connexion.

- `sudo nmcli connection edit <conn>` (`nmcli c e <conn>`) : menu interactif pour éditer une interface
    * `?` : afficher les commandes disponibles
    * `goto <categorie>` : changer de catégorie
        * `goto ipv4`
    * `back` : revenir à la catégorie parente
    * `desc <objet>` :
        * `desc <setting>` : expliquer ce que fait un paramètres, quelles sont les valeurs possibles et quelle est la valeur par défaut
            * Ex : `desc dns`
        * `desc <category>` : montrer tous les paramètres disponibles dans la catégorie
            * Ex : `desc ipv4`
    * `set <setting> <valeur>` : modifier un paramètre
        * Ex : `set dns 8.8.8.8 8.8.4.4`
    * **`save` : appliquer les modifications**
    * `quit`
- `sudo nmcli connection modify <conn> <setting-full-path> <value>` : set un paramètre directement sans passer par le menu interactif
    * `sudo nmcli c m "Ethernet 1" ipv4.dns "8.8.8.8 8.8.4.4"`
</details>

+ Il peut être utile de __redémarrer le service *NetworkManager*__, par exemple après une modification de ses fichiers de configuration.
    - `sudo systemctl restart NetworkManager`
    - Pour les systèmes qui se configurent en *DHCP*, redémarrer *NetworkManager* permet de rafraîchir votre *lease DHCP*
+ Pour dépanner des problèmes de connexion, il peut être utile de jeter un œil aux __logs de NetworkManager__ : `sudo journalctl -xeu NetworkManager`

*NetworkManager* est très complet et peut aussi gérer des connexions complexes (bond LACP, VLANs ...)


### Autres méthodes de configuration réseau permanente

<details><summary>Sur les distributions récentes, <b>NetworkManager est LA méthode de configuration réseau à privilégier.</b></summary>

Toutefois, vous pouvez être amenés à utiliser d'autres méthodes pour créer des connexions persistentes :
+ [`netplan`](https://doc.ubuntu-fr.org/netplan) d'Ubuntu
    - (Configure en coulisses des connexions NetworkManager)
+ [`/etc/network/interfaces`](https://www.malekal.com/etc-network-interfaces-configurer-le-reseau-sur-debian/) de Debian
+ [`/etc/sysconfig/network-scripts`](https://www.cyberciti.biz/faq/how-to-configure-a-static-ip-address-on-rhel-8/) sur d'anciens RHEL

</details>

</details>


## 2.3.D - Config du resolver DNS (resolv.conf)
<details><summary><code>/etc/resolv.conf</code> contient la config du resolver DNS</summary>

+ Sa syntaxe est très simple et vous pouvez la modifier manuellement
    - `nameserver <ip>` : Adresse d'un serveur DNS. 3 maximum, du plus prioritaire au moins prioritaire.
    - `search <base-domain...>` : Domaines de recherche pour les noms courts.
        * Par exemple, avec `search utt.fr assos.utt.fr`, `charcutt` va d'abord être interprété comme `charcutt.utt.fr` et en cas d'échec comme `charcutt.assos.utt.fr`.
        * `domain <base-domain>` fait la même chose, mais ne peut avoir qu'une valeur.
    - `timeout <sec>` : Délai au bout duquel essayer un autre serveur DNS en cas d'échec.
        * Par défaut, 5 secondes
+ Votre OS utilise probablement *NetworkManager*, et dans ce cas, c'est lui qui gère le contenu de ce fichier. Les modifications seront perdues au redémarrage de *NetworkManager*.
    - Un commentaire en début de fichier vous prévient si c'est le cas

</details>


## 2.3.E - Outils de dépannage
<details><summary>Quelques outils qui peuvent s'avérer pratiques pour quand Google est cassé...</summary>

+ `ping` : test de connectivité IP - **la base pour tester la couche 3**
    - `-6` : IPv6
    - `-I` : Interface source
    - `-c <count>` : s'arrêter au bout de *count* tests
+ `traceroute` : tracer le chemin vers un hôte
    - Utilise le port 33434/udp, qui n'est pas forcément ouvert sur les firewalls. Il est donc souvent impossible d'avoir le chemin complet.
    - Vous pouvez utiliser un autre port [UDP, voire un port TCP](https://stackoverflow.com/questions/10995781/trace-a-particular-ip-and-port), pour augmenter les chances de tracer le chemin complet
+ `nc <host> <port>` : netcat - client/serveur TCP/UDP brut - **utile pour tester la couche 4**
    - `nc  22` : tester que le port SSH est ouvert et écoute
    - `sudo nc -l -p 636` : mode serveur, écoute sur 636/tcp
        * Très pratique pour tester si un pare-feu bloque un port entre le serveur et le client.
            * Ici, le client se connecterait avec `nc mons.lab-linux.local 636`
        * *NB : Il faut les droits d'admin pour écouter sur un port <1024*
    - `nc` n'est pas forcément installé sur votre système.
+ `curl` : envoi d'une requête à un serveur Web
+ `tcpdump`, `wireshark` : sniffage de paquets
    - `sudo tcpdump [-vAn] -i <iface> [filtre]` : décrire les headers des paquets
        * Exemple : `sudo tcpdump -n -i lo "tcp port 80"`
        * `-v` : afficher plus d'informations sur les paquets
        * `-A` : afficher la charge utile des paquets
        * `-n` : ne pas résoudre les noms de domaine, afficher directement les IP source et destination
    - Vous pouvez utiliser [`tcpdump` pour faire une capture de paquets](https://linuxexplore.com/2010/05/30/remote-packet-capture-using-wireshark-tcpdump/) et l'analyser ensuite avec Wireshark. Pratique pour analyser les paquets d'un serveur sans interface graphique.
+ `ss` : ports utilisés sur le système local. **Utile pour vérifier si un service s'exécute bel et bien, et écoute sur les bonnes IP / le bon port**
    - `-t` : TCP
    - `-u` : UDP
    - `-l` : listening (sans, montre les ports SOURCE utilisés)
    - `-n` : Afficher les numéros de ports au lieu des noms de service
    - `-a` : tous les sockets
    - Exemple : `ss -atln` : quels sont les ports TCP sur lesquels j'écoute ?
+ `nmap` : [scan de ports](https://nmap.org/book/port-scanning-tutorial.html) sur un système distant / découverte d'hôtes sur tout un réseau
    - Souvent pas installé par défaut
+ `iptraf` : Statistiques d'utilisation TCP/IP, sur un menu interactif facile à utiliser.
    - Non installé par défaut
  
</details>

## Exercices

### Exercice 1 : iproute2 (très facile)
<details>

+ Affichez les interfaces réseau disponibles. Affichez les routes disponibles. Repérez l'interface avec laquelle vous accédez à Internet.
+ Ajoutez une deuxième adresse IP sur cette interface réseau, dans le même subnet que l'adresse IP actuelle.
+ Pingez cette nouvelle IP. Vous devez avoir des réponses.
+ Supprimez votre route par défaut et reconfigurez-la manuellement avec `iproute2`. Vous devez pouvoir ping `8.8.8.8`.
</details>

### Exercice 2 : NetworkManager (facile)
<details>

+ Si vous avez obtenu vos paramètres réseau automatiquement, utilisez `nmtui` pour les redéfinir manuellement (en adressage statique)
+ Avec `nmcli`, listez vos connexion puis affichez les détails de la connexion que vous avez modifiée.
+ Inspectez le fichier de configuration de la connexion *NetworkManager*. En éditant ce fichier, modifiez le nom de la connexion. Si vous avez deux serveurs DNS de configurés, inversez leur ordre. Appliquez les changements.
+ Vérifiez que le nom de la nouvelle connexion a changé avec `nmcli`.
+ Affichez les logs de `NetworkManager`
</details>

### Exercice 3 : Couche 4 (modéré)
<details>

Vous avez besoin d'une machine cliente et d'un serveur distant capable de recevoir des connexions SSH. *(Si votre serveur est une VM tournant sur un système hôte, le client peut très bien être le système hôte.)*

Pour un client Windows, il faudra télécharger `ncat` et `nmap` sur [https://nmap.org/](https://nmap.org/).

+ A l'aide de `netcat` / `ncat` / `nc`, lancez une écoute sur le port 2024/tcp de votre serveur.
    - Si besoin, ouvrez ce port dans votre firewall.
    - Si besoin, créez une règle de redirections de ports pour que votre client soit en mesure d'atteindre ce serveur.
+ Toujours depuis le serveur, dans un autre terminal, affichez les ports TCP en écoute.
+ Depuis le client, maintenant, utilisez `nmap` pour scanner les ports TCP ouverts sur le serveur. Vous devez voir le port 2024.
+ Encore depuis le client, à l'aide de `netcat` / `ncat` / `nc`, envoyez un message à votre serveur. Le serveur doit recevoir le message.
</details>

### Exercice 4 : tcpdump (intermédiaire)
<details>

Vous avez besoin d'une machine cliente avec une interface graphique, et un serveur distant capable de recevoir des connexions SSH. *(Si votre serveur est une VM tournant sur un système hôte, le client peut très bien être le système hôte.)*

+ Sur le serveur, lancer un `watch -n 10 curl toastytech.com/evil/index.html`
+ Depuis le client, capturer les paquets HTTP et DNS sur le serveur distant pendant 1 minute. Puis, interrompre `watch` sur le serveur.
+ Côté client, analyser les [paquets avec Wireshark](https://www.it-connect.fr/le-suivi-dune-connexion-tcp-avec-wireshark/).
    - [Explications détaillées (TP de RE04)](https://drive.google.com/file/d/1scP4gicq6XUY3n617jVEfT3khFQSlrAO/view?usp=drive_link)
</details>