# 1.2 - Commandes de base
Tuto niveau débutant à intermédiaire. On a quand même mis quelques exercices corsés pour ceux qui commencent à toucher leur bille.

Il y a beaucoup de commandes alors **n'essayez pas de tout apprendre par cœur** - voyez le plutôt comme une *"cheatsheet"*. On vous conseille de lire ce qui vous intéresse, faire les exercices et revenez voir les commandes si vous avez un doute.

## Pour découvrir / s'entraîner
+ [GameShell](https://github.com/phyver/GameShell) : Un mini-jeu qui vous fait utiliser toutes ces notions. Le mieux pour découvrir et mettre en pratique. Si vous arrivez au dernier niveau, vous pouvez considérer que c'est acquis pour vous.

## 1.2.1 Gestion des fichiers

### Systèmes de fichiers
<details><summary>Sur Linux, <b>tout est fichier</b>.</summary>

Que ce soient les comptes d'utilisateurs, la configuration de votre shell, votre clavier ou votre connexion avec un serveur distant, tout est représenté par des fichiers.

</details>

<details><summary>Les fichiers et répertoires sont gérés par des <b>systèmes de fichiers</b> (<i>File Systems</i>).</summary>

Un FS est une couche logique qui opère par dessus une partition d'un périphérique de stockage. 

Son rôle principal est d'organiser vos données dans une structure logique arborescente, qui vous permet de les désigner par des **chemins**.

Le FS a d'autres tâches comme [gérer les permissions](#les-permissions-unix) sur ces données, vérifier leur intégrité, prendre des instantanés à des fins de sauvegarde ... et bien d'autres encore. 

</details>

<details><summary>Linux "monte" tous les systèmes de fichiers connus sur <b>une unique arborescence</b>.</summary>

+ La **racine** de cette arborescence est `/`.
    - C'est la base de l'arborescence.
    - Il s'agit généralement du système de fichiers sur lequel le gros du système est installé.
+ D'autres systèmes de fichiers peuvent être **"montés"** en n'importe quel point sous la racine.
    - *Monter* (*mount*) un système de fichiers signifie le *"brancher"* à l'arborescence pour rendre son contenu accessible au système.
    - Un *mountpoint* est un répertoire sur lequel on monte un système de fichiers.
        * Par exemple, votre partition de boot est probablement montée sur `/boot/efi`.
+ Certains répertoires de l'arborescence ont une fonction standard, par exemple :
    - `/etc` : fichiers de config globaux (agissant à l'échelle de tout le système)
    - `/home` : répertoire sous lequel se trouvent les répertoires de chaque utilisateur (sauf *root*)
    - `/tmp` : fichiers temporaires ...
    - Ces chemins ne sont pas spécialement intuitifs mais vous apprendrez rapidement à vous y repérer pour trouver rapidement ce que vous cherchez.

</details>

#### Commandes de gestion de l'arborescence
<details><summary><b>Commandes pour manipuler l'arborescence du système</b></summary>

+ `pwd` : afficher le chemin du **dossier courant**
+ `ls` : **lister le contenu du dossier** courant.
    - `ls <dossier>` : lister le contenu d'un dossier
    - `ls <fichier>` : lister un fichier
    - `-l` : détails
        * permissions ...
        * taille ...
        * date de modification ...
    - `-a` : aussi les fichiers cachés (commençant par `.`)
        * *Remarque : tous les dossiers contiennent deux dossiers cachés spéciaux : `.` et `..`.* 
            * `.` désigne le dossier courant lui-même
            * `..` désigne le dossier parent.
+ `cd` : __*change directory*__
	- `cd <dossier>` : se rendre dans *dossier*.
		* Si le chemin est **absolu** (commence par `/`), on suivra le chemin à partir de la racine de l'arborescence.
    		* *Exemple : `cd /var/logs` : je vais dans le dossier `var/logs` sous `/`*
  		* Si le chemin est **relatif** (ne commence pas par `/`), on suivra le chemin à partir du dossier courant.
    		* *Exemple : `cd logs` : je vais dans le dossier `logs` sous le dossier courant*
	- `cd ..` : se rendre dans le dossier parent
	- `cd -` : retourner au dossier précédent
	- `cd ~` ou `cd $HOME` : se rendre dans son répertoire utilisateur.
+ `mkdir <dossier...>` : __*make directory*__
	- Même logique que *cd* avec les chemins relatifs et absolus
	- Pour créer plusieurs niveaux de répertoires d'un coup, utiliser `-p`:
		* `mkdir -p titi/tata/toto`
+ `rm` : __*remove*__
	- **<u>!-- ATTENTION --!</u>**, commande dangereuse. Les éléments supprimés ne sont pas placés dans une corbeille, ils sont perdus définitivement.
	- `rm <fichier...>`
	- `rm -r <dossier...>` : supprime les dossiers et leur contenu
	- Pour ne pas demander de confirmation, `-f`
		* `rm -rf titi`
	- Vous pouvez sélectionner les fichiers et dossiers avec un *wildcard* :
		* `?` : n'importe quel caractère
		* `*` : n'importe quelle chaîne de caractères
		* *Exemple : `rm *.jpg` supprime tous les fichiers qui termine par '.jpg'*
+ `cp <source...> <destination>` : __*copy*__, `mv <source...> <destination>` : __*move*__
	- Pareil, vous pouvez utiliser des *wildcards*
	- `cp 2021.tar.gz 2022.tar.gz 2023.tar.gz /mnt/stockage/photos/`
	- `mv /home/Documents/* /mnt/stockage/documents/`
+ `touch <fichier...>` : créer un fichier vide, ou rafraîchir sa date de modification s'il existe déjà
	- Alternative : `:> <fichier>` écrit du vide dans un fichier - donc, crée un fichier vide ou supprime son contenu s'il existe déjà.
+ `ln -s <source> <destination>` : créer un *symlink* (lien symbolique)
	- _Avec un chemin absolu : `ln -s /usr/lib/jvm/java-17-openjdk /usr/lib/jvm/default` : créée le lien `/usr/lib/jvm/default`, un pointeur sur `/usr/lib/jvm/java-17-openjdk`._
		* _Même si vous déplacez `default` ailleurs, il pointera toujours sur les mêmes données._
	- _Avec un chemin relatif : `ln -s ./java-17-openjdk ./default`._
		* _Si vous déplacez `default` dans un autre dossier, il tentera de pointer sur `java-17-openjdk` dans ce nouveau dossier._
	- Il existe aussi des *hard links* (sans l'option `-s`).
+ `locate <nom-ou-chemin>` et `updatedb` : **Trouver des fichiers n'importe où dans l'arborescence**
	- `sudo updatedb` : pour rafraîchir la base de données des chemins
	- `locate openjdk` : trouver tous les chemins qui contiennent *"openjdk"*
	- `locate jvm/java-17-openjdk` : trouver tous les chemins qui contiennent *"jvm/java-17-openjdk"*...
	- `locate -b <nom>` : chercher uniquement dans le nom de fichier et pas le reste du chemin
	- Si vous n'avez pas ces commandes, procurez-vous le paquet *mlocate* ou *plocate*
+ `du` : *disk usage* (taille d'un fichier ou répertoire)
	- `du <fichier...>` : taille de fichiers
	- `du -csh <dossier>` : taille totale du dossier
	- `du -ch <dossier>` : taille de chaque élément dans le dossier, puis taille totale
+ `df` : Espace libre et utilisé sur l'ensemble d'un système de fichiers
	- `df -h` : pour tous les FS montés
	- `df -h <chemin>` : pour le FS contenant le chemin

#### Pour aller plus loin :
<details>

+ [*tar* : créer et extraire des archives rapidement](https://doc.ubuntu-fr.org/tar)
+ [*Hard links* and *Symlinks* explained](https://www.redhat.com/sysadmin/linking-linux-explained)
+ [La commande *find*](https://www.ionos.com/digitalguide/server/configuration/linux-find-command/)
	- Chercher des fichiers avec des filtres bien spécifiques
	- Eventuellement exécuter des actions sur ces fichiers
</details>

</details>
</details>


### Permissions
<details><summary>Les fichiers et répertoires sont associés à des <b>permissions</b> à des fins de contrôle d'accès.</summary>

+ Les permissions possibles sont :
    - `r` : __*read*__
    - `w` : __*write*__, le droit de modifier ou supprimer.
        * *Sur un dossier, accorde le droit de créer des fichiers dans ce dossier.*
    - `x` : __*execute*__
        * *Sur un dossier, accorde le droit de se rendre dans ce dossier et d'en lister le contenu.*
+ Les fichiers ont un **utilisateur et un groupe propriétaire**. Les permissions s'appliquent à **trois entités** :
    - `u` : __*user*__, l'utilisateur propriétaire du fichier/dossier
    - `g` : __*group*__, les utilisateurs du groupe propriétaire du fichier/dossier
    - `o` : __*others*__, tous les autres utilisateurs
+ Les permissions d'un fichier pour une entité donnée sont représentées par **trois bits** - on peut donc les traduire en valeur numérique dans le système octal.
    - Dans l'ordre, les trois bits sont `rwx`. 
    - Avec ces trois **bits à 1 (permission accordée)**, on a donc `111` = `7` dans le système octal.
        * `r` = 4 (2²)
        * `w` = 2 (2¹)
        * `x` = 1 (2⁰)
        * `rwx` =  4 + 2 + 1 = 7
    - *Par exemple, si j'accorde uniquement les droits de lecture et d'exécution, mais pas d'écriture, les permissions s'écrivent :*
        * `r-x` en notation symbolique
        * `5` en notation octale

<details><summary>Pour récapituler, voici quelques exemples :</summary>

+ `rwx rwx rwx` ou `777` :
    - `u=rwx` : l'utilisateur propriétaire a tous les droits
    - `g=rwx` : le groupe propriétaire a tous les droits
    - `o=rwx` : tous les autres utilisateurs ont tous les droits
    - Le fichier est donc publiquement lisible, modifiable et exécutable.
+ `rwx r-x r-x` ou `755` :
    - Tout le monde peut lire et exécuter
    - Seul l'utilisateur propriétaire peut modifier
+ `rwx r-x ---` :
    - L'utilisateur propriétaire et le groupe propriétaire peuvent lire et exécuter.
    - Seul l'utilisateur propriétaire peut modifier
+ `rw- r-- r--` :
    - Tout le monde peut lire.
    - Seul l'utilisateur propriétaire peut modifier.
    - Le fichier n'est pas exécutable.

</details>

+ L'utilisateur `root` peut passer outre les permissions. Il a **tous les droits sur le système**. L'administreur peut temporairement agir en temps que `root` en préfixant votre commande par **`sudo`** - mais, il fait alors très attention à ce qu'il fait !
	- Exemple : `sudo cat /etc/shadow`
	- Réfléchissez avant d'exécuter des commandes avec `sudo`. Par exemple, `sudo rm -rf /` supprimerait TOUTE l'arborescence du système.

#### Commandes de gestion des permissions
<details>

+ `ls -l` : afficher
	- Affiche l'utilisateur et le groupe propriétaires
	- Affiche les permissions
+ `chmod <permissions> <fichier-ou-dossier...>` : changer les permissions
	- Les permissions peuvent être assignées pour seulement certaines entités ou pour toutes les entités, de façon absolue ou relative et en notation symbolique ou octale :
		* `chmod u+x monf.txt` : **ajouter** la perm `x` à `u`
		* `chmod ug=rw,o= monf.txt` : **set de manière absolue** les perms à `rw-` pour `u` et `g`, set les perms à `---` pour `o`
		* `chmod ugo-x monf.txt` : **retirer** la perm `x` à tout le monde
		* `chmod rwxr-xr-x monf.txt` : **set de manière absolue** les perms `rwx` pour `u`, `r-x` pour `g` et `r-x` pour `o`
		* `chmod 755 monf.txt` : équivalent à la commande du dessus. **<u>C'est la syntaxe à privilégier</u>**
	- Option `-R` : mode récursif (appliquer à tous les enfants d'un dossier)
+ `chown [user][:group] <fichier-ou-dossier...>` : changer l'*ownership*
	- `chown dupont monf.txt`
	- `chown dupont:admin monf.txt`
	- `chown :admin monf.txt`
	- Option `-R` : mode récursif (appliquer à tous les enfants d'un dossier)
</details>

#### <h4>Pour aller plus loin : gestion avancée des permissions

<details><summary>Le système des permissions UNIX, détaillé plus haut, est évidemment trop simpliste pour mettre en place des politiques de contrôle d'accès complexes. Linux utilise aussi d'autres systèmes de permissions plus avancés.</summary>

+ [Permissions spéciales](https://www.redhat.com/sysadmin/suid-sgid-sticky-bit) : sticky bit, SGID bit et SUID bit
+ [ACLs](https://doc.ubuntu-fr.org/acl) : Aller plus loin que le modèle POSIX standard pour assigner des permissions spécifiques à plusieurs utilisateurs et groupes
+ [SElinux (avancé)](https://access.redhat.com/documentation/fr-fr/red_hat_enterprise_linux/9/html/using_selinux/getting-started-with-selinux_using-selinux) : confinement avancé des composants du système
    - Très utilisé sur les systèmes RHEL

</details>

</details>

### Exercices : fichiers et permissions
<details>

#### Exercice 1 : Baptême du feu (très facile)
<details>

+ Affichez votre répertoire courant.
+ Rendez-vous dans le dossier `/tmp`.
+ Listez son contenu, y compris les fichiers cachés.
+ Affichez la taille totale du dossier `/tmp`.
+ Créez-y un dossier `mond`
+ Déplacez-vous dans ce dossier et créez-y un fichier vide `monf`
+ Remontez au dossier parent.
+ Supprimez le dossier `mond` en une seule commande.
+ Rendez-vous dans votre *home directory*.
+ Affichez les permissions et l'utilisateur propriétaire du dossier courant.
+ Sans changer de dossier, créez le fichier `/opt/monf-le-retour-de-la-vengeance-chez-les-chtis-4`
+ Créez un lien symbolique vers le fichier nouvellement créé dans votre home directory.
+ En utilisant `locate`, trouvez tous les chemins du système de fichier qui contiennent *"vmlinuz"*
+ En utilisant `locate`, trouvez tous les chemins du système de fichier qui contiennent `tab` dans le nom du fichier (et pas le reste du chemin)
+ En utilisant `locate`, trouvez `monf-le-retour-de-la-vengeance-chez-les-chtis-4`, le fichier que vous avez récemment créé mais dont vous avez oublié le chemin complet.

</details>

#### Exercice 2 : Vous ne passerez pas (facile)
<details>

+ Créez un fichier `helloworld.sh` contenant :
	- ```bash
		#!/bin/bash
		echo 'H3ll0 W0rLd !!!'
		```
	- Pour écrire dans le fichier, utilisez [cat](#ecrire-avec-cat) ou un [éditeur de texte en lignes de commandes](#editeur2texte)
+ Affichez les permissions du nouveau fichier.
+ Exécutez ce fichier en lançant `./helloworld.sh` dans le dossier où vous l'avez créé.
+ Donnez la permission d'exécution au groupe propriétaire et à tous les autres utilisateurs, mais supprimez le droit de lecture à votre groupe.
+ Remplacez le groupe propriétaire du fichier par *wheel* ou *sudo* (selon celui des deux qui apparait lorsque vous tapez `groups`)
+ Créez en une seule commande les dossiers `/mnt/toto/titi/tata`. Affichez les propriétaires du dossier `/mnt/toto/titi/tata`. Si vous n'en êtes pas propriétaire, devenez propriétaire de tous les dossiers se trouvant sous `/mnt/toto` en une seule commande.

</details>

#### Exercice 3 : Find (intermédiaire)
<details>

+ Trouvez les fichiers à partir de `/var/log` qui finissent par *".log"*.
+ Trouvez les dossiers dans `/etc` qui contiennent *"network"*, de façon insensible à la casse, dans leur nom.
+ Trouvez tous les fichiers dans `/var/log` qui datent de moins de 24h.
+ Trouvez tous les symlinks sous `/usr/lib` en descendant au maximum d'un répertoire en dessous de `/usr/lib`
	- (chercher dans le dossier-même et dans ses dossiers enfants, mais pas plus loin)
+ Trouvez tous les fichiers qui ne possèdent pas la permission d'exécution pour tous les utilisateurs (*others*) dans `/usr/bin` et, avec la même commandes, symlinkez les vers le dossier `/opt/vip/` que vous aurez créé au préalable. Les symlinks ne doivent pas être cassés.
</details>

#### Exercice 4 : Permissions spéciales (intermédiaire)
<details>

+ Créez un utilisateur *toto*
+ Créez un répertoire public `/opt/collab`, avec les fichiers suivants et les permissions suivantes :
	- `/opt/collab/by-toto.txt` appartenant à l'utilisateur *toto*
	- `/opt/collab/by-me.txt` appartenant à votre utilisateur
	- Tout le monde doit pouvoir modifier le contenu de ces deux fichiers, mais le seul à pouvoir supprimer ces fichiers est leur propriétaire
		* Votre utilisateur doit pouvoir modifier le contenu du fichier de *toto*, mais pas le supprimer
  
+ Créez un groupe *team*
+ Créez un répertoire `/opt/workspace` appartenant au groupe *team* où tous les fichiers qui seront créés à l'avenir seront automatiquement placés dans le groupe *teams*
	- Créez un fichier avec *toto* dans ce groupe. Il doit être placé automatiquement dans le groupe *teams*.
  
+ Seul *root* est autorisé à changer des mots de passe. Comment se fait-il que la commande `passwd` vous permette de changer votre propre mot de passe sans être *root* ?

+ Trouvez, à l'aide de `find`, tous les fichiers dans `/bin`, `/usr/bin` et `/sbin` qui ont au moins le bit SUID ou SGID de set. 
</details>

#### Exercice 5 : ACLs (intermédiaire)
<details>

+ Créez un utilisateur *toto*.
+ Créez avec votre utilisateur le fichier `/opt/just-me-and-toto`, et donnez-lui les permissions *0600*.
+ Sans changer l'owner du fichier ni ses permissions standard, créez une règle d'ACL pour que ce fichier soit aussi lisible et modifiable par *toto*.
+ *toto* et votre utilisateur doivent pouvoir afficher et modifier le fichier. Personne d'autre ne doit pouvoir l'afficher ou le modifier.

*NB : En pratique, les ACLs sont souvent utilisées pour partager des sockets entre plusieurs utilisateurs applicatifs.*
</details>

#### Exercice 6 : Magic FileSystem (avancé)
<details>

Vous aurez besoin des paquets qui permettent la gestion d'un système de fichiers BTRFS, probablement nommés `btrfs-progs`.

+ Créez un disque virtuel de 5G et montez le sur un nouveau *loop device*
	- *Indice: `man truncate`, `man losetup`*
+ Créez deux partitions de 2.5G et formattez-les avec le système de fichiers *BTRFS* de façon à simuler du RAID0 (mirroring des deux partitions)
	- *Indice: `man mkfs.btrfs`*
+ Montez le système de fichiers en lecture seule sur `/mnt/butter`. Vous ne devez pas pouvoir créer un fichier dessus, même en temps que *root*.
+ Remontez le système de fichiers en mode écriture.
+ Créez un fichier `/mnt/butter/toto.txt` 
+ Faites un snapshot de votre système de fichiers à `/mnt/butter/snap-$(date +%Y-%m-%d)`
+ Supprimez le fichier `/mnt/butter/toto.txt`. Le snapshot doit toujours contenir `toto.txt`
+ *Hardlinkez* le fichier `toto.txt` contenu par le snapshot vers votre *home*. Pourquoi cela ne fonctionne-t-il pas ?
+ Démontez le système de fichiers, supprimez le *loop device* et le disque virtuel

</details>


#### Exercice 7 :  SELinux (avancé, RHEL)
<details>

+ Si ce n'est pas déjà fait, activez SELinux de façon permanente sur le système.
+ Affichez le contexte SELinux de `/etc/passwd`
+ Faites tourner votre serveur SSH sur le port 2222/tcp au lieu du port standard 22/tcp.
+ Avec un serveur web Apache, servez des fichiers depuis un sous-répertoire de votre *home* par exemple `~/Documents`
+ Suivez la procédure de récupération de mot de passe.
	- Démarrez en ajoutant `rd.break` à vos options de kernel. Vous obtiendrez un shell juste après le chargement du *initial RAMdisk*.
	- Montez le FS de votre partition racine sur `/sysroot`.
	- Chrootez sur `/sysroot` et modifiez le mot de passe d'un utilisateur.
	- Au cours de la procédure, il se peut que vous ayez à un moment fait subir des modifications au contexte SElinux d'un fichie. Lequel ?
    	- Arrangez-vous pour régler le problème.
+ (Difficile) : Créez un script [dispatcher](https://man.archlinux.org/man/NetworkManager-dispatcher.8.en) *NetworkManager* (script qui se lance automatiquement lorsqu'un certain événement survient sur une connexion NM).
	- Ce script DOIT commencer par `#!/bin/bash`
	- Lorsqu'une connexion devient UP, il doit append l'heure dans le fichier `/opt/connection-up.log`
	- Normalement, ça ne doit pas marcher. Qu'est-ce qui peut bien empêcher ce script de se lancer ? Résoudre le problème.

<details><sumamry><i>Indices :</i></summary>

- [Listening on uncommon ports with SELinux](https://www.rootusers.com/use-selinux-port-labeling-to-allow-services-to-use-non-standard-ports/)
- [Serving uncommon files with Apache on an SElinux-enabled system](https://tecadmin.net/configure-selinux-for-apache-new-directory/)
- [SElinux autorelabel a filesystem](https://access.redhat.com/solutions/24845)
  
</details>

</details>
</details>





## 1.2.2 Shell et traitement de texte

### Shell
<details><summary>Un <i>shell</i> est un <b>interpréteur de commandes</b> grâce auquel vous interagissez avec le système.</summary>

Faisons l'analogie entre Linux et une noix : vous avez le cerneau (*kernel*) à l'intérieur (assure les fonctions bas niveau), et la coquille (*shell*) qui l'enveloppe (interface haut niveau avec laquelle interagissent les utilisateurs).

Le shell par défaut sur la plupart des distributions est `bash` - c'est un shell très complet, à la syntaxe assez simple, et qui peut tout à faire servir de langage de programmation avec des variables, fonctions, boucles et tests conditionnels.

Pour les tâches avancées, on fait appel à d'autres programmes que l'on lance à partir du shell. 

<details><summary>Le shell assigne à ces programmes trois descripteurs de fichiers :</summary>

+ Une **sortie standard** (`stdout`, descripteur de fichier 1)
	* Résultat produit par la commande
+ Une **sortie d'erreur** (`stderr`, descripteur de fichier 2)
	* Messages d'information, de debug ou d'erreur qui ne doivent pas se retrouver mélangés avec le résultat de la commande
+ Une **entrée standard** (`stdin`, descripteur de fichier 0)
	* C'est là que le programme lit les données d'entrée dont il se nourrit. Ne pas confondre avec les arguments et les options fournis à l'appel du programme.

</details>

#### Redirections

<details><summary>Lorsque vous lancez les programmes de façon dans votre terminal, par défaut, <i>stdout</i> et <i>stderr</i> sont affichés dans le terminal et <i>stdin</i> provient de vos frappes de clavier. Or, le shell propose des <b>redirections</b> pour orienter la sortie d'une commande vers un certain fichier ou pour la passer à un autre programme.</summary>

+ `macommande > output.txt` : redirige *stdout* dans un fichier. S'il existe déjà, **remplace** son contenu.
  - `macommande >> output.txt` : redirige *stdout* dans un fichier. S'il existe déjà, **ajoute** le résultat à son contenu.
  - `macommande 2> err.txt` : redirige la sortie d'erreur dans un fichier.
  - `macommande 2>/dev/null` : `/dev/null` est un fichier spécial qui sert de "trou noir" - la sortie d'erreur disparaît tout simplement.
  - `macommande 2&>1 >/dev/null` : Redirige le descripteur `2` vers `1`, et `1` vers `/dev/null`. En gros, et *stdout* et *stderr* disparaissent, pour une commande totalement silencieuse.
+ `macommande < input.txt` : lit les données d'entrée à partir d'un fichier au lieu de les lire interactivement.
  - `macommande <<< "les chaussettes de l'archiduchesse sont elles sèches ? Je sais pas mais en tout cas elles fouettent vachement"` : input à partir d'un texte passé directement à la ligne de commande plutôt qu'un fichier
+ `macommande1 | macommande2` : opérateur **pipe** : passe les données produites par `macommande1` à `macommande2`. Vous pouvez enchaîner plusieurs commandes comme ça.
  - `macommande2 < <( macommande1 )` : équivalent, permet d'écrire les commandes dans l'autre sens.

</details>

#### Variables
<details>

+ Vous pouvez définir une variable avec la syntaxe `variable=valeur`. Ensuite, référencez la variable avec `$variable`.
	- Il ne doit pas y avoir d'espace entre le `=` et les deux opérandes.
	- Lire la valeur d'une variable : `echo "$variable"`
	- Si la valeur contient des espaces, il faut la mettre entre *quotes* simples ou doubles (`'` ou `"`).
  		* `couleurs="rouge vert jaune"`
  		* `$couleurs` serait décomposée en trois arguments - pour la traiter comme une seule chaîne de caractères, il faut aussi la référencer entre *quotes* : `echo "$couleurs"`
+ Scope des variables
    - `variable=valeur macommande` : définir la variable seulement pour une commande.
    - `export variable=valeur` : définir la variable pour tous les sous-processus (variable d'environnement). Autrement, elle reste locale au shell.
		* Une variable ne peut pas être exportée dans les processus parents. Elle ne peut que "descendre" la hiérarchie des processus.
+ Shell substitution : enregistrer la valeur d'une commande
	- `variable="$(macommande)"` : enregistre le *stdout* de *macommande* dans *variable*
	- `if [ "$(macommande)" = "rouge vert jaune" ]; then echo rasta; fi` : utilise le résultat de *macommande* directement comme argument d'une autre commande, sans passer par une variable.
+ Le shell utilise aussi des variables spéciales. Voici les plus importantes :
	- `$?` (code d'erreur de la dernière commande exécutée).
        * `0` : terminée sans erreur.
        * Toute autre valeur : erreur.
	- `$PATH` : dossiers où chercher des exécutables, délimités par des *":"*.
		* Exemple : `/usr/local/bin:/usr/bin:/bin:/usr/local/sbin`
		* Lorsque vous lancez une commande sans spécifier son chemin absolu (par exemple `ls`), vous cherchez parmi les dossiers des `$PATH` un exécutable qui porte le nom `ls`.
		* Vous pouvez ajouter un dossier comme suit : `export PATH="$PATH:/path/to/mon/dossier"`
	- `$HOME` : home directory de l'utilisateur courant.
    	* Bash supporte aussi les raccourcis `~` et `~dupont` (pour le home d'un autre utilisateur nommé *dupont*)
	- `$USER` et `$UID` : nom et UID de l'utilisateur courant

</details>

#### Navigation
<details>

- `Tab` : autocomplétion
- `Ctrl+L` : nettoyer l'écran.
- `Ctrl+A` / `Début` : début de ligne. `Ctrl+E` / `Fin` : fin de ligne.
- `Alt+RetourArrière` : effacer un mot entier


Bash se souvient des commandes que vous avez tapées précédemment.
+ `↓/↑` : naviguer dans les commandes précédentes.
+ `Ctrl+R` : chercher une commande précédente par mot clef.
- `!!` : commande précédente
  - Par exemple, `sudo !!` pour répéter la commande précédente en tant que `root`
  - `!3` : il y a 3 commandes
  - `!:2` : argument 2 de la dernière commande
  - `!3:2` : argument 2 il y a 3 commandes
  - `!$` : dernier argument de la dernière commande
	* Exemple : `cat /mnt/stockage/Documents/toto.txt` puis `cp !$ ~/Documents`
+ `history` : historique des commandes tapées. 
	- Ne contient pas les commandes actuellement en mémoire - seulement celles qui sont sauvegardées une fois que vous quittez le shell avec un `exit`.
</details>  

#### Pour aller plus loin
<details>

Aller plus loin avec Bash :
+ [Boucles](https://ryanstutorials.net/bash-scripting-tutorial/bash-loops.php)
+ Tests conditionnels
	- [`if`, `test` et `[`](https://buzut.net/maitriser-les-conditions-en-bash/)
	- [`&&` et `||`](https://frnn.medium.com/understanding-and-in-linux-bash-navigating-command-sequences-like-a-pro-fe5e72489da1)
	- [`case`](https://linuxize.com/post/bash-case-statement/), [`getopts`](https://www.quennec.fr/book/export/html/341)
+ [Fonctions](https://www.it-connect.fr/les-fonctions-en-bash%EF%BB%BF/) et [Aliases](https://doc.ubuntu-fr.org/alias)
+ [Personnalisation avec .bashrc](https://borntocode.fr/personnaliser-son-environnement-de-travail-sous-linux-grace-a-son-bashrc/) : enregistrer des variables, fonctions et alias de façon permanente
+ [Commentaires](https://linuxize.com/post/bash-comments/)

[Zsh](https://linuxhandbook.com/why-zsh/) : non, c'est pas le shell de Zemmour, c'est un shell à la syntaxe proche de Bash mais avec plus de fonctionnalités !
</details>

</details>


### Traitement de texte

<details><summary>Même si cela ne paraît pas évident, un sysadmin est souvent amené à travailler avec des outils de traitement de texte en lignes de commandes.</summary>

Puisque vous utilisez une interface textuelle, vous êtes en effet souvent amenés à filtrer, remplacer, générer et interpréter du texte.

Nous allons survoler les commandes les plus utiles pour travailler avec du texte.

+ `cat <fichier...>` : **Afficher un ou plusieurs fichiers**
	- On peut aussi l'utiliser pour **écrire dans des fichiers** grâce à des redirections : <a id=ecrire-avec-cat></a>
		* `cat > toto.txt` : créer / remplacer toto.txt
    		* Le "fichier" d'entrée est *stdin*, donc votre entrée clavier dans le terminal. Tapez interactivement votre message. Quand vous avez fini de taper le message, `Ctrl+D` pour fermer.
  		* `cat >> toto.txt` : append dans toto.txt
+ `wc` : *word count* (**Compter** des lignes/mots/caractères)
	- `wc -l` : nombre de lignes
	- `wc -c` : nombre de caractère
    	* *NB : (Compte les sauts de ligne !)*
	- `wc -w` : nombre de mots
	- Exemple : `who` affiche un utilisateur par ligne. Pour avoir le nombre d'utilisateurs connectés, il suffit donc de faire un `who | wc -l`
+ `grep <regex> <fichier...>` : **filtrer par expression régulière** (*regex*). **Incontournable !**
	- `grep 'root:' /etc/passwd` : afficher uniquement les lignes de `/etc/passwd` qui contiennent le texte `root:`
	- `getent passwd | grep 'root:'` : piper `grep` pour filtrer le résultat d'une commande
	- `-B<n>` : (Before) Afficher aussi les `n` lignes avant le match
	- `-C<n>` : **(Contexte) Afficher aussi les `n` lignes avant et après le match**
	- `-A<n>` : (After) Afficher aussi les `n` lignes après le match
    	* `man sshd_config | grep -A2 Banner`
	- `-i` : insensible à la casse
	- `-v` : mode inversé (lignes qui ne matchent pas)
	- `-E` : mode regex étendu (plus de fonctionnalités)
	- `-R` : chercher dans le contenu de tous les enfants du dossier (mode récursif)
		* souvent utilisé avec `-l` (affiche uniquement le nom des fichiers qui match)
	- Quelques tokens spéciaux pour écrire des *regex* :
		* `^` / `$` : début / fin de ligne
        	* `grep "^ip" /etc/services` : Les lignes de `/etc/services` qui **commencent** par *"ip"*
    	* `.` : n'importe quel char
    	* `[abc]` : n'importe quel char parmi *"a"*, *"b"* et *"c"*
        	* `[a-zA-Z0-9]` : on peut spécifier des portées
        	* `[[:alpha:]]` : on peut spécifier des classes de caractères (Mode étendu `-E`)
        	* `[^abc]` : n'importe quel char sauf *"a"*, *"b"* et *"c"*
    	* `a*` : 0 ou plus fois le caractère *"a"*
    	* `a+` : 1 ou plus fois le caractère *"a"* (Mode étendu `-E`)
    	* `{n}` : n fois exactement le caractre *"a"* (Mode étendu `-E`)
    	* `{n,}` / `{,n}` : n fois ou plus / n fois ou moins le caractre *"a"* (Mode étendu `-E`)
    	* `{m,n}` : entre `m` et `n` fois le caractère *"a"* (Mode étendu `-E`)
    	* `(plusieurs tokens)` : groupe, que l'on peut ensuite rappeler ou répéter (Mode étendu `-E`)
    	* *Utiliser un antislash `\` pour échapper un caractère spécial*
+ `head`, `tail` : **n premières / dernières lignes d'un texte**
	- `getent hosts | head -3` : 3 premières lignes de `/etc/hosts`
	- `getent hosts | tail -3` : 3 dernières lignes de `/etc/hosts`
	- `getent hosts | tail +3` : Tout sauf les 3 premières lignes de `/etc/hosts`
	- *Par défaut, si vous ne spécifiez pas `-<n>`, sélectionne 10 lignes.*
+ `vi` et `nano` : <u>**éditeurs de texte**</u> en lignes de commandes, souvent installés par défaut. <u>**Indispensable.**</u> <a id=editeur2texte></a>
	- [`nano`](https://www.hostinger.fr/tutoriels/nano) est facile d'utilisation mais assez simple
	- [`vi`](https://www.linuxtricks.fr/wiki/guide-de-sur-vi-utilisation-de-vi) est difficile d'utilisation mais très complet. Une fois maîtrisé, il peut vous rendre très productif.
		* Si vous l'avez, préférez `vim` qui dispose du *syntax highlighting* pour la plupart des langages et fichiers de config
	- Apprenez à utiliser l'un des deux.
+ `more`, `less`, `view` : pagers (naviguer dans un texte qui déborde de l'écran)
	- `more` et `less` sont simples, vous pouvez scroll avec ↓↑ et la barre d'espace. `q` pour quitter.
	- `view` est un `vi` en mode lecture seule, donc compliqué mais beaucoup de fonctionnalités avancées (recherche, raccourcis clavier pour naviguer...)
+ `cut -d<delim> -f<champs>` : sélectionner les **champs d'un texte**.
	- `cat /etc/passwd | cut -d: -f2,3,7` : champs 2, 3 et 7 de `/etc/passwd`, avec le délimiteur `:`
	- Si votre délimiteur est un espace, il faudra le mettre entre quotes pour que bash ne l'interprète pas comme du vide. `cut -d' ' ...`
	- `-f3-` : sélectionne Tous les champs à partir du champ 3.

### Pour aller plus loin
<details>

+ [`sort`](https://www.redhat.com/sysadmin/sort-command-linux) : **trier** les lignes
	- *Exemple : `ls -l | sort -n -k5`  : contenu du dossier, trié par taille croissante*
+ [**`sed`**]**(https://quickref.me/sed.html) : traitement de texte avancé par expressions régulières. **Incontournable**.
	- _Exemple : `sed '1,$s/:/|/g'` remplace les *":"* par des *"|"* sur la première et la dernière ligne._
+ [xargs](https://www.malekal.com/comment-utiliser-commande-xargs-exemples/) : convertir l'entrée standards en arguments à passer à une commande

D'autres commandes plus gadget :

+ [`awk`](https://connect.ed-diamond.com/GNU-Linux-Magazine/glmf-131/awk-le-langage-script-de-reference-pour-le-traitement-de-fichiers) : traitement de texte avancé par langage procédural et expressions régulières.
	- _Exemple : `awk -F':' '{print $2 $NF}` affiche le deuxième et dernier champ de `/etc/passwd` (en utilisant *":"* comme délimiteur)._
+ [`rev`](https://bash.cyberciti.biz/guide/Rev_command) : inverser le texte
+ [`tr`](https://www.malekal.com/la-commande-tr-utilisations-et-exemples/) : *transliterate* (supprimer ou remplacer des classes de caractères)
	- _Exemple : `tr '[a-z]' '[A-Z]'` mettra tous les caractères entre *"a"* et *"z"* en majuscules._
+ [`split` & `paste`](https://www.lessons2all.com/linux_split_paste_command.php) : scinder & joindre des fichiers
</details>
</details>

### Exercices : Shell et traitement de texte
<details>

#### Exercice 1 : Secrétaire d'élite (modéré)
<details>

+ Affichez uniquement les services qui utilisent un port UDP dans `/etc/services`
+ Affichez uniquement les adresses IPv4 (et non IPv6) dans `/etc/hosts`
+ Affichez le contenu de `/etc/ssh/sshd_config` sans les commentaires
+ Affichez les services qui contiennent un numéro à la fin de leur nom dans `/etc/services`
+ Affichez uniquement le nom d'utilisateur et le shell utilisé dans `/etc/passwd`. On ne doit pas voir les autres champs.
	- Filtrez maintenant ce résultat pour montrer tous les utilisateurs qui utilisent le shell `nologin`.
+ Donnez le nombre de lignes dans `/etc/passwd`
+ Affichez tout sauf les 3 premières lignes de `/etc/nsswitch.conf`
+ Affichez seulement les lignes 2 et 3 de `/etc/nsswitch.conf `
+ Avec votre éditeur de texte préféré, rajoutez un message en dessous du message actuel dans `/etc/issue` et sauvegardez les changements.
+ Concaténez tous les fichiers du dossier `/lib/udev/rules.d/` en un seul fichier `/tmp/udev-rules-dump`
+ Affichez les lignes de `/etc/ssh/ssh_config` qui contiennent *"host"*, de manière insensible à la casse, ainsi que les 2 lignes suivant chaque occurrence.
+ En utilisant `grep` et sans passer *root*, affichez le nom de tous les fichiers à partir de `/etc` qui contiennent *"network"* (pas dans leur nom, dans leur contenu), de manière insensible à la casse, et faites en sorte de ne pas afficher les messages d'erreur.
+ Enregistrez dans la variable `hosts_lookup_strategy` la ligne de `/etc/nsswitch.conf` qui commence par *"hosts:"*. Puis, affichez le nombre de mots dans cette variable en retirant d'abord le premier champ.

</details>

#### Exercice 2 : Sed du grenier (intermédiaire)
<details>

+ En une seule commande `sed`, affichez uniquement les lignes 3, 5 et la dernière ligne de la sortie de `getent passwd`
+ Affichez avec `sed` uniquement les lignes de `/etc/services` qui ont des numéros de ports d'au maximum 3 chiffres
+ Utilisez `sed` pour afficher `/etc/ssh/sshd_config` sans les lignes vides
	- Bonus : faites la même chose avec `tr`
+ Remplacez toutes les occurences du mot *"network"*, quelle que soit leur casse, par le mot *"Rozo"* dans la sortie de `man NetworkManager`
+ Enregistrez la sortie de la commande `man NetworkManager` dans un fichier. Maintenant, appliquez la commande `sed` précédente pour venir remplacer directement le contenu de ce fichier (sans en créer un nouveau).
+ Utilisez `sed` pour afficher d'abord le numéro de port (sans le *"/tcp"*, *"/udp"* ...), pu`/etc/services`,
+ (Un peu plus difficile) Toujours avec `sed`, afficher dans la sortie de `man man`, uniquement le texte qui était entouré par des chevrons `<>`. Les chevrons eux-mêmes ne doivent pas s'afficher, seulement leur contenu, et les lignes qui ne contenaient pas de mots entre chevrons ne doivent pas non plus s'afficher.

</details>
</details>




## 1.2.3 Gestion des utilisateurs & groupes
<details>

+ `useradd <username>` : **ajouter un utilisateur**
	- `-g <maingrp>` : groupe principal. Par défaut, un groupe avec le même nom que l'utilisateur est créé.
	- `-G <groups...>` : groupes secondaires
	- `-d </path/to/home>` : chemin de son home directory si vous ne voulez pas utiliser le `/home/<username>` par défaut.
	- `-s </path/to/shell>` : spécifier un login shell différent du `/bin/bash` par défaut
+ `usermod <username>` : **modifier un utilisateur**
	- `-aG <groups...>` : **rejoindre des groupes secondaires** (très utile)
		* *NB: Si vous vous ajoutez vous-même dans un groupe, il faut quitter votre shell et en relancer un nouveau pour que le changement prenne effet.*
		* *Exemple : s'ajouter dans le groupe `docker` pour avoir le droit de gérer des conteneurs `docker` avec votre utilisateur.*
	- `-l <login>` : modifier le nom d'utilisateur
	- `-L <login>` : lock (désactiver le compte)
+ `passwd [username]` : **modifier le mot de passe d'un utilisateur**
	- (Si aucun utilisateur spécifié, modifier son propre mot de passe)
+ `userdel <username...>` : supprimer des utilisateurs

+ `su - [username]` : se connecter en tant qu'un autre utilisateur. Il faudra rentrer son mot de passe
	- Par défaut, si aucun utilisateur n'est spécifié, vous devenez *root*. Attention !
	- `logout` ou `exit` : se déconnecter du shell de l'autre utilisateur
+ `sudo -u <username> <commande...>` exécuter une seule commande en tant qu'un autre utilisateur

+ `groupadd <groups...>` : **ajouter des groupes**
+ `groupdel <groups...>` : supprimer des groupes

+ `groups [username]` : **nom des groupes secondaires**
+ `id [username|uid]` : UID, GID du Groupe principal et GIDs des groupes secondaires
	- `id -ng [username]` : nom du groupe principal
	- `id -nG [username]` : **nom des groupes secondaires (équivalent à la commande `groups`)**
+ `who` : **utilisateurs connectés actuellement**
+ `last` : last logins

+ `/etc/passwd` : Fichier contenant les informations des comptes d'utilisateur. Voir [https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)
+ `/etc/group` : Fichier contenant les informations des groupes. Voir [https://www.cyberciti.biz/faq/understanding-etcgroup-file/](https://www.cyberciti.biz/faq/understanding-etcgroup-file/)
+ `/etc/shadow` : Fichier contenant les hash de mots de passe des comptes d'utilisateur. Accessible à `root` uniquement.

</details>

### Exercices : Gestion des utilisateurs & groupes
<details>

#### Exercice 1 : Amis imaginaires (facile)
<details>

+ Créez l'utilisateur *titi* avec les paramètres suivants :
	- Son *home directory* est `/var/titi`
	- Son shell est `/bin/sh`
	- Son mot de passe est *"tatatoto"*
+ Créez le groupe *amisimaginaires*
+ Ajoutez *titi* au groupe *amisimaginaires*, sans modifier son groupe principal. Vérifiez qu'il a rejoint le groupe dans le fichier `/etc/group`.
+ Créez le fichier `/tmp/partagé`. Changez son groupe propriétaire en *amisimaginaires*. Donnez au groupe toutes les permissions, et aux autres aucune permission.
+ Connectez-vous en tant que *titi*. Ecrivez quelque chose dans ce fichier. Dans un autre terminal, affichez les utilisateurs connectés en ce moment.
+ Déconnectez-vous du shell de *titi*. Lisez le fichier avec votre utilisateur.
+ En tant que *titi*, supprimez ce fichier, sans vous reconnecter sur son shell.
+ Supprimez *titi* et le groupe *amisimaginaires*.
</details>
</details>

## 1.2.4 Gestion des processus & ressources
<details><summary>Tous les programmes gérés par le noyau s'exécutent sous la forme de <b>processus</b>. Il faut pouvoir suivre ces processus et les interrompre quand ça ne va pas.</summary>

+ `ps aux` : **afficher tous les processus** en cours et leurs détails (PID, utilisation du CPU ...)
	- On filtre souvent avec `grep`
+ `htop`, `top` : **surveiller de façon interactive** les **processus** et les **performances CPU/mémoire**
+ `uptime` : charge moyenne des CPU sur les 1, 5 et 15 dernières minutes
+ `free -h` : **mémoire dispo et utilisée**
+ `w` : temps de CPU (PCPU) utilisé par chaque utilisateur connecté

+ `pgrep <pattern>` : trouver les **PIDs d'un processus à partir d'un mot** ou d'une regex
	- Ex: `pgrep systemd`
	- `-l` : afficher la commande du processus en plus du PID
	- `-u <user>` : chercher les processus d'un utilisateur donné
		* `pgrep -l -u admin` : tous les processus de l'utilisateur *"admin"*
+ `kill`, `killall` et `pkill` : **envoyer un signal** à des processus (le plus souvent **pour les arrêter**)
	- `kill -SIGTERM <PID...>` : terminer "gentiment" des processus (pour qu'ils se terminent correctement)
	- `kill <PID...>` : terminer "méchamment" (tuer) des processus (forcer à quitter quand ils ne répondent pas)
	- `killall <commande>` : chercher les processus par nom et les tuer
    	* `killall firefox` 
    	* `-u` : seulement pour mon utilisateur
    	* `-SIGTERM` : terminer "gentiment"
	- `kill $(pgrep <pattern>)` : tuer par pattern
	- `pkill -u <username>` : tuer tous les processus d'un utilisateur.
    	* *NB : Si vous tuez tous vos processus alors que vous êtes connecté en SSH, vous perdrez votre connexion.*
	- `pkill -P <PID>` : tuer le processus et tous ses processus enfants
+ Gérer les processus lancés par ce shell :
	- `macommande &` : __lancer *macommande* en tache de fond__ et récupérer une invite de commande
		* Exemple: `sleep 20 &`
	- `jobs` : jobs en cours dans ce shell et leur *jid* (job ID)
	- `Ctrl+Z` : Mettre en pause le processus
	- `Ctrl+C` : Interrompre le processus
	- `bg %<jid>` : reprendre le processus de job ID *"jid"* en tâche de fond
	- `fg %<jid>` : faire revenir un processus au premier plan et le reprendre s'il était arrêté
		* Exemple : ```bash
				sleep 20
				[ctrl+z]
				jobs
				bg %1
				fg %1
				[ctrl+c]
			```
	- `kill %<jid>` : terminer un processus avec son Job ID
	- `$!` : PID du dernier processus lancé
+ `nohup <commande> &` : **Lancer un processus qui survivra même si le shell est fermé / la connexion est perdue**
	- Sans `nohup`, votre processus serait interrompu dès que vous quitteriez votre shell ou perdez votre connexion au serveur.
	- Exemple : `nohup bash make-backup.sh &` 
	- *NB : `tmux` fait la même chose.*
</details>

### Exercices : Gestion des processus et des ressources
<details>

#### Exercice 1 : Tueur en série (facile)
<details>

+ Lancez la commande `sleep 200` en tâche de fond.
+ Identifiez son JID.
+ Tuez le processus à partir de son JID
+ Lancez la commande `sleep 200` et mettez la en pause.
+ Repérer le PID processus en utilisant `ps` ou `pgrep`
+ Mettre fin correctement au processus en utilisant `kill`.
+ Lancer 10 fois la commande sleep en tâche de fond avec : `for ((i=0;i<10;i++)); do sleep 200 &; done`.
+ Tuer en une seule commande toutes les instances de `sleep` en utilisant `killall` ou `pgrep` + `kill`.
</details>

#### Exercice 2 : Parano (facile)
<details>

+ Visualisez en live vos processus. Repérez les processus les plus gourmands en CPU.
+ En moyenne, votre système a-t-il eu plus de boulot au cours de cette dernière minute qu'au cours des 15 dernières minutes ?
+ Affichez la RAM disponible.
</details>

#### Exercice 3 : Fourchette bombée (avancé)
<details>
+ Créez une *fork bomb* en une seule ligne de bash.

</details>
</details>

## 1.2.5 Obtenir de l'aide
<details><summary>Vous vous sentez peut-être perdu face à toutes ces commandes et à tous ces fichiers à "connaître par cœur". Rassurez-vous, même ceux qui utilisent Linux depuis le berceau ne les connaissent pas sur le bout des doigts : le plus important, c'est de <b><u>savoir où trouver de l'aide</u></b>.</summary>

+ `man <page>` : afficher le <u>**manuel**</u>
	- `man kill` : manuel d'une commande
	- `man sshd_config` : manuel d'un fichier de config
+ Beaucoup de commandes ont une option `--help` qui présente la <u>**syntaxe et les options disponibles**</u>

**<u>`man` et l'option `--help` seront vos meilleurs amis pour la vie.</u>**

+ `apropos <keyword>` : trouver les *manpages contenant un mot-clef
	- Exemple : `apropos filesystem`
+ `/usr/share/doc` : fichiers de doc (souvent, la doc pour des serveurs est bien fournie)

Quand on débute, on a tendance à foncer chercher sur Internet et à oublier que de l'aide en lignes de commandes existe. Habituez-vous à utiliser l'aide en ligne de commandes, qui peut apporter des réponses précises et rapides.


</details>

### Exercices : obtenir de l'aide
<details>

#### Exercice 1 : RTFM (ez peezy)
<details>

+ Quelle option de `df` permet, par soucis de lisibilité, d'afficher la taille des systèmes de fichiers en KiloOctets, MegaOctets, GigaOctets ... plutôt que toujours en octets ?
+ À quoi sert la commande `lpr` ?
+ Quelle directive d'un timer systemd permet d'ajouter une composante aléatoire au timer ?
	- *Indice: `apropos`, `grep -i`, `grep -i -C<n>`*
</details>
</details>

