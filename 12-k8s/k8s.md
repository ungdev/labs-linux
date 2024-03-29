# 12.1 Introduction à Kubernetes

Kubernetes est un orchestrateur de conteneurs open-source qui permet d'automatiser le déploiement, la mise à l'échelle et la gestion des applications conteneurisées. Il a été conçu par Google et est maintenant maintenu par la Cloud Native Computing Foundation (CNCF).

## 12.1.1 Pourquoi Kubernetes ?

**Kubernetes** est un outil développé à l'origine par Google pour pouvoir orchestrer des conteneurs Docker. Le but ce cet outil est de simplifier la gestion des conteneurs, en automatisant les tâches de déploiement, de mise à l'échelle et de gestion des applications conteneurisées.  

Vous avez vu comment gérer plusieurs conteneurs docker à l'aide de **docker compose**, vous avez aussi vu qu'il était possible de faire du **loadbalancing** avec Traefik pour quelques conteneurs. Mais que se passe-t-il si vous avez des **dizaines, des centaines ou des milliers** de conteneurs à gérer ? C'est là que Kubernetes entre en jeu.

L'idée de Kubernetes est d'avoir des fichiers de configuration qui décrivent l'état désiré de votre application -- à la manière d'un `docker-compose.yml` -- , et Kubernetes se charge de faire en sorte que l'état actuel de votre application corresponde à l'état désiré.

## 12.1.2 Architecture de Kubernetes
> Ça semble compliqué, mais ça va allez, on va prendre le temps de bien comprendre, d'être le plus clair, et de faire des exercices pour bien comprendre.