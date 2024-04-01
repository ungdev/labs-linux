# 12 - Haute disponibilité et scalabilité

Vous connaissiez déjà les bases de l'administration système, et vous avez appris à mettre en place des architectures microservices avec Docker. Vous êtes maintenant prêts à passer à l'étape suivante : la haute disponibilité et la scalabilité.

On va utiliser Kubernetes pour la mise en place de l'architecture microservices.

Niveau avancé

+ [12.1 - Introduction à Kubernetes](k8s.md#121-introduction-à-kubernetes)
    - [12.1.1 - Pourquoi Kubernetes ?](k8s.md#1211-pourquoi-kubernetes-?)
    - [12.1.2 - Architecture de Kubernetes](k8s.md#1212-architecture-de-kubernetes)
      - [12.1.2.A - Principe d'un cluster Kubernetes](k8s.md#1212a-principe-dun-cluster-kubernetes)
      - [12.1.2.B - Composants d'un cluster Kubernetes](k8s.md#1212b-composants-dun-cluster-kubernetes)
        - [Pods](k8s.md#pods)
        - [Services](k8s.md#services)
        - [Ingress](k8s.md#ingress)
        - [ConfigMap et Secrets](k8s.md#configmap-et-secrets)
        - [Volumes](k8s.md#volumes)
        - [Deployments](k8s.md#deployments)
        - [ReplicaSets](k8s.md#replicasets)
        - [StatefulSets](k8s.md#statefulsets)
        - [DaemonSets](k8s.md#daemonsets)
        - [Jobs](k8s.md#jobs)
        - [CronJobs](k8s.md#cronjobs)
+ [12.2 - Application pratique](pratique.md#122-application-pratique)
    - [12.2.1 - Installation de Kubernetes](pratique.md#1221-installation-de-kubernetes)
    - [12.2.2 - Découverte de kubectl et Minikube](pratique.md#1222-découverte-de-kubectl-et-minikube)
    - [12.2.3 - Déploiement d'une application](pratique.md#1223-déploiement-dune-application)
    - [12.2.4 - Mise à l'échelle de l'application](pratique.md#1224-mise-à-léchelle-de-lapplication)
<details><summary><i>Contributeurs :</summary>

+ Noë Charlier [noe.charlier@utt.fr](mailto:noe.charlier@utt.fr)
</details>
