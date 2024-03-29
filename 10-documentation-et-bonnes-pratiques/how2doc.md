<div style=align:center><h1>10.1 - How To Doc ?</div>

<img src=img/bonnedoc-mauvaisedoc.png align=right height=400px>

La doc du SI précédent était devenue chaotique. On a donc souhaité profiter de la refonte du SI pour repartir sur de bonnes bases.

Notre objectif est de centraliser et codifier la doc, pour qu'elle garde une certaine cohérence et reste utilisable sur le long terme. Voilà pourquoi nous écrivons cette "doc de la doc".

## 10.1.1 Les dix commandements

|                          |                                                                                                                                                                        |
|--------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RELECTURE                | **Te relire**, en te mettant à la place d'**un lecteur qui ne connaît rien à ton projet** tu devras.
| VÉRIFICATION DES MANIPS  | Tu n'oublieras point de **tester commandes et procédures**.
| ANTICIPATION DES PROBLÈMES |  Penser à tous les **problèmes qui peuvent éventuellement survenir** et aux cas particuliers il faudra.
| STRUCTURATION | Ta doc en parties et en étapes tu **structureras**. Un **sommaire** tu fourniras.
| CONCISION | **À l'essentiel** tu iras.
| ACTUALISATION | Lorsqu'évolueront les données et procédures concernant ton service ou ton infrastructure, **ta doc mettre à jour** tu devras.
| COHÉRENCE | **Après toute modification**, à la cohérence de l'ensemble tu veilleras.
| EXPLICATIONS, EXEMPLES & ILLUSTRATIONS | Lorsque l'évidence de tes enseignements douteuse te paraîtra, des **explications, exemples, schémas ou captures d'écran** tu fourniras.
| RÉFÉRENCES | À la **qualité et à la pérennité de tes références** tu veilleras.
| SÉCURITÉ | Des **secrets** du SI tu ne révèleras aucun. |

Et bien sûr - *mais c'était évident et surtout ça faisait 11 si je le mettais aussi dans le tableau* - tu n'oublieras pas de signer ta doc en indiquant comment te contacter, ni de renseigner les éventuelles autres personnes concernées.

## 10.1.2 Structure type
Par soucis de cohérence, chaque doc doit, dans les grandes lignes, suivre le plan suivant. 

Ne pas hésiter à rajouter des sous-parties. Pour des docs longues, ne pas hésiter à split en plusieurs fichiers.

<details>

+ **README**
    - Brève présentation du projet
    - But
    - Auteurs et personnes concernées avec leurs coordonnées de contact
+ **Sommaire**
+ *(Facultatif)* **I. Vue d'ensemble**
    - Par exemple, pour un projet en lien avec l'infra réseau, un gros schéma récapitulatif de l'archi logique avec les IP, un schéma de l'archi physique ...
    - Expliquer le fonctionnement du service, son architecture, le rôle de chacun de ses composants ...
+ *(Facultatif)* **II. Choix de la solution**
    - Expliquer pourquoi cette solution avait été choisie par rapport à d'autres
+ **III. Déploiement**
    - Pour un service, comment le déployer à l'identique
    - Pour un projet d'infra, comment il a été mis en place dans le détail, de la commande à la configuration en passant par l'installation.
+ **IV. Administration**
    - Procédures pour la vie de tous les jours de la solution, quand tout va bien :
        * Comment ajouter un nouvel utilisateur...
        * Comment faire une mise à jour ...
        * Comment renouveler les certificats ...
        * Comment activer telle feature...
        * Comment modifier les principaux paramètres...
            * *Ne pas détailler les paramètres osef, seulement ceux que l'on peut avoir besoin de modifier*
+ **V. Maintenance et dépannage**
    - Erreurs fréquentes et comment les régler
    - Procédures à suivre en cas d'incident
        * Il faut imaginer quels incidents peuvent arriver, et détailler la procédure à mettre en œuvre pour les résoudre. Cette procédure doit être testée.
        * Pour une solution avec plusieurs composants impliqués, penser à ce qui peut se passer pour chaque composant qui peut déconner
        * Quand il faut éteindre / débrancher des composants, dire quelles précautions sont à prendre et quelles autres parties de l'infra seront potentiellement impactées
        * Une sous partie par incident
    - Quels logs regarder et que chercher dedans
    - Outils de troubleshooting
+ *(Facultatif)* **Références**
    - Des liens vers une doc, un forum qui vous a aidé, avec une brève explication d'en quoi ça peut aider

</details>

Un exemple est également celui de la doc choisie pour le nouveau SIA:

<details><summary>Doc du SIA</summary>

#### Choix d'un IPAM

L'IPAM (IP Address Management) est un outil qui permet de gérer les adresses IP d'un réseau.  
Nous avons choisit **NetBox**.

#### Choix de l'outil de documentation

Le choix de l'outil de documentation à été un choix difficile, car il fallait trouver un outil qui soit simple d'utilisation, mais qui permette de faire des docs complexes.

Nous avons fini par choisir **Wikijs**.

#### Forme de la doc

Deux guides:
- Un guide utilisateur
    - Fait pour un utilisateur final (l’étudiant UTT lambda)
- Un guide administrateur
  - (catégorie Architecture) Expliquer le SIA (dans son ensemble)
    - Vive les schéma !!!!!! (Exqualidro)
    - On fait dedans le listing des déploiements (c’est à dire la liste des VM, la liste des serveurs PX etc…)
    - Conventions de nommages
    - Comment on fait des déploiements (avoir un ensemble cohérent, des procédures) ? Très général ! (pas par service)
      - Processus de décision entre VM/Kube : par définition, on déploie sur Kube, sauf quelques rares cas que l’on définit ici. Et du coup, on explique pk on a choisi Kube pour tout déployer.
- Un service/outil (genre Kubernetes, dolibarr, Proxmox…)
  - Etat des services (aka liste des services, pour quels assos, déployé où et quand) 
  - Comment déployer
  - Comment maintenir
  - Comment supprimer

    > **Attention:** Les états (comprend le où, quand, comment, et changelog) qui concernent l’architecture (liste des VM par exemple) serait fait par la partie architecture, la liste des services (quels dolibarr) serait fait dans la partie service/outil.  La limite serait placé au point de vue utilisateur final.

- Guide de la documentation
- Audit de la doc par l’extérieur (comment kon fait)


</details>


## 10.1.3 Considérations de forme
<details>

+ La doc doit être rédigée en **Markdown**.
    - Vous pouvez utiliser des balises HTML si besoin.
+ **Numéroter les parties** et sous-parties.
    - Rester cohérent tout au long de la doc.
    - Exemples :
        * `III.1.B.a)` : Forte insistance sur la hiérarchie des parties. `III` est une giga-macro-partie, `a)` est le dernier niveau de détails. 
        * `3.1.2.A` : Très bien pour un document à la structure sémantique plus aplatie.
+ User et abuser des listes imbriquées. Premier niveau : `+`
    - Deuxième niveau: `-`
        * Troisième niveau et plus : `*`
+ Les lignes de commande et fichiers de config doivent toujours être entre **backticks** (\`) ou **triple-backticks** (\`\`\`). Si du syntax highlighting est possible, l'utiliser.
    - <u>**Pas de screens des commandes et fichiers de config**</u> - il faut pouvoir tout copier-coller - à moins que ce soit vraiment pour illustrer avec un exemple ou montrer le résultat d'une commande.
    - Quand la forme générique d'une ligne de commandes est compliquée, donner un exemple. 
        * *Par exemple, si j'explique `nmcli c[onnection] m[odify] <conn> set <setting> <value>`, je peux donner l'exemple `nmcli c m "Ethernet 1" set ipv4.dns "8.8.8.8 8.8.4.4"`*
+ Mettre autant de **cross-references** que possible (liens vers une une autre partie de la doc / vers une autre doc)
+ Mettre en gras ou souligner les passages importants. Mettre en italique les passages "bonus".
    - Quand une manip est risquée, le signaler de manière bien visible, par exemple : **!-- ATTENTION --!**
+ Utiliser les balises `<details>` et `<summary>` pour faire des sections dépliantes quand on veut donner une explication, une justification, ou tout autre encart qui "coupe" la lecture.
+ Pour citer la doc officielle, des paroles ou des conseils reçus : utiliser une *blockquote* : `>`

</details>

## 10.1.4 Remarques sur le SIA

Le SIA (système d'information des associations) est l'infrastructure proposée aux associations et club de l'UNG.
Depuis 2024, cette dernière est en cours de refonte. La doc du SIA est donc en cours de rédaction.

Les raisons sont diverses mais on surtout cité une **mauvaise documentation** une **maintenance difficile** et une **sécurité insuffisante**, et ceux pour plusieurs raisons :
 - A l'époque le SIA était géré par peu d'étudiant, qui ont fini par partir.
 - Le problème de la **transmission des compétences**, et d'une très mauvaise documentation de qui a été fait.
 - Les **technologies** beaucoup trop récente et prématuré pour être utilisé dans un cadre de production.
   - C'est un très gros problème, car les étudiants n'ont pas forcément les compétences pour gérer ces technologies par la suite, même si cela reste un très bon terrain d'apprentissage.
   - C'est pourquoi durant le construction du nouveaux SIA nous avons décidé de **simplifier** au maximum les technologies utilisées, pour que les étudiants puissent facilement reprendre le projet, ainsi que la création d'un **labs** pour que les étudiants puissent apprendre à gérer ces technologies avant de les utiliser en production, et également tester les **nouvelles** technologies.

### La **documentation** ne fait pas tout !

En effet, la documentation est un élément clé pour la maintenance et la transmission des compétences, mais elle ne fait pas tout. Il est important de **transmettre** les compétences, et de **former** les étudiants à la gestion de ces technologies.  
La documentation est seulement un support très technique, qui ne permet pas de comprendre les enjeux et les problématiques de l'infrastructure fondamentalement. Elle n'a **pas** pour but de former les étudiants.  
C'est aussi pourquoi il faut justifier l'utilisation des technologies, et expliquer pourquoi elles ont été choisies.  

Il faut également proposer des supports de formation, comme celui-ci, pour que les étudiants puissent apprendre à gérer ces technologies.