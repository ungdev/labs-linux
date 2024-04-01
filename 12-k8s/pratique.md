# 12.2 - Application pratique

Dans cette partie, nous allons mettre en pratique les connaissances acquises dans la partie précédente. Nous allons installer Kubernetes, déployer une application et la mettre à l'échelle.  

Pour apprendre nous allons utilisé [Minikube](https://minikube.sigs.k8s.io/docs/), un outil qui permet de créer un cluster Kubernetes local.

En production je vous conseille d'utiliser [k3s](https://k3s.io/), une distribution légère de Kubernetes.

## 12.2.1 - Installation de Kubernetes

Pour installer Minikube, suivez les instructions sur le site officiel : [https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)

## 12.2.2 - Découverte de kubectl et Minikube

Pour commencer, nous allons découvrir les commandes de base de `kubectl` et `minikube`.  

Pour utiliser la commande `kubectl`, sans passer par `minikube`, vous pouvez ajouter l'alias suivant dans votre fichier `.bashrc` ou `.zshrc` :

```bash
alias kubectl="minikube kubectl --"
```

On peut lancer un cluster Kubernetes local avec la commande suivante :

```bash
minikube start
```

## 12.2.3 - Déploiement d'une application



## 12.2.4 - Mise à l'échelle de l'application