# 8.1 - Introduction à l'architecture microservices

## 8.1.0 Qu'est-ce qu'une architecture microservices

<details><summary>Principe <b> général </b> d'une architecture microservices</summary>

Un microservice est une application qui est conçue pour être déployée et gérée de manière indépendante. Chaque microservice est une application autonome qui peut être déployée, mise à jour, et équilibrée de manière **indépendante**. Les microservices sont généralement conçus pour être **petits et spécialisés**, et ils communiquent entre eux via des API. 

On le compare souvent à une architecture monolithique, où toutes les fonctionnalités sont regroupées dans **une seule application**. Les microservices permettent de découper une application en plusieurs services indépendants, ce qui facilite la maintenance, le déploiement, et l'évolutivité de l'application. 

Jusqu'à présent, nous avons principalement travaillé avec des applications monolithiques, mais les microservices sont de plus en plus populaires, car ils permettent de développer des applications plus flexibles, évolutives, et résilientes.

<img alt="Schéma micro service vs monolithique" src="img/microservice.png" width="70%">

</details>

#### Pourquoi utiliser une architecture microservices ?

- **Flexibilité** : Les microservices permettent de découper une application en plusieurs services indépendants, ce qui facilite la maintenance, le déploiement, et l'évolutivité de l'application.
- **Évolutivité** : Les microservices permettent de développer des applications plus flexibles, évolutives, et résilientes.
- **Résilience** : En cas de panne d'un microservice, les autres services continuent de fonctionner normalement.
- **Déploiement continu** : Les microservices permettent de déployer et mettre à jour des services de manière indépendante.
- ...

> Nous allons voir dans les prochaines parties comment **Docker** peut vous aider à mettre en place une architecture microservices.

## 8.1.1 Introduction à Docker

Docker est une plateforme open-source qui permet de **développer**, **déployer**, et **exécuter** des applications dans des conteneurs. 

<details><summary>C'est quoi un <b> conteneur </b> ?</summary>

Un conteneur est une unité logicielle qui contient une application et toutes ses dépendances. Les conteneurs sont légers, portables, et sécurisés, et ils permettent d'isoler une application de son environnement. 

Les conteneurs sont similaires aux machines virtuelles, mais ils sont plus légers et plus rapides à démarrer. Les conteneurs partagent le noyau (*kernel*) de l'hôte, ce qui les rend plus efficaces en termes de ressources. (*Bien évidemment, ça n'empêche pas les *kernel panic*...*)  

<img alt="VM vs Docker" src="img/docker-vs-vm.png" width=70%>

Il existe plusieurs technologies de conteneurisation, mais Docker est l'une des plus populaires. (et son deamon containerd)

* De façon plus fondamentale, docker utilise un outil appelé `containerd` pour gérer les conteneurs. Ce *runtime* est responsable de la création, de la gestion, et de la destruction des conteneurs. Ce dernier est lui-même géré par un *deamon* appelé `dockerd`.  

   * `containerd` est un *runtime* de conteneurs open-source qui fournit une interface de bas niveau pour gérer les conteneurs, pour cela il vient interfacer avec le *kernel* de l'hôte en utilisant les fonctionnalités de conteneurisation du *kernel* Linux telles que `cgroups` et `namespaces`. Il le fait à travers la librairie `libcontainer` appelée par `runc`.
     * Les `cgroups` permettent de limiter les ressources utilisées par les conteneurs, tandis que les `namespaces` permettent d'isoler les processus et les ressources des conteneurs, ce sont des fonctionnalités du *kernel* Linux.
</detail>