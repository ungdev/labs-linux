# 8.2 Docker Compose

## 8.2.1 Les bases de Docker Compose

<details><summary>Détails</summary>

Docker compose permet de définir et de gérer des applications multi-conteneurs. Il permet de définir les services, les réseaux et les volumes dans un fichier YAML.  

Il permet donc de configurer les paramètres d'un conteneur, de définir les dépendances entre les conteneurs et de les lancer en une seule commande, d'avoir plusieurs conteneurs.

#### La structure de base d'un fichier `docker-compose.yml`

```yaml
version: '3.8'

x-common-varibles:

services:
  service1:
    image: image1
    ports:
      - "8080:80"
    volumes:
      - /path/to/volume:/container/path
    networks:
      - network1
    depends_on:
      - service2

  service2:
    image: image2
    ports:
      - "8081:80"
    volumes:
      - /path/to/volume:/container/path
    networks:
      - network1

networks:
    network1:
        driver: bridge

volumes:
    volume1:
        driver: local
```

Explications:
- `version`: la version de la syntaxe de Docker Compose
- `services`: les services à lancer
- `service1`, `service2`: les noms des services
- `image`: l'image à utiliser
- `ports`: les ports à exposer
- `volumes`: les volumes à monter
- `networks`: les réseaux à utiliser
- `depends_on`: les dépendances entre les services
- ...
  
#### Les commandes de base

- `docker compose up`: pour lancer les services
- `docker compose down`: pour arrêter les services
- `docker compose ps`: pour afficher les services
- `docker compose logs`: pour afficher les logs des services
- `docker compose exec`: pour exécuter une commande dans un service
- ...

Il existe aussi des options (comme avec la commande docker):

- `docker compose up -d`: pour lancer les services en arrière-plan
- `docker compose up --build`: pour construire les images avant de lancer les services
- ...

> Vous pouvez consulter la documentation officielle de Docker Compose pour plus d'informations.
> [Docker Compose](https://docs.docker.com/compose/)

</details>

<details><summary>Exercices</summary>

<details><summary>Exercice 1</summary>

Créez un fichier `docker-compose.yml` pour lancer un serveur web (Nginx) et un serveur de base de données (MySQL).

<details><summary>Solution</summary>

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - /path/to/volume:/usr/share/nginx/html
    networks:
      - network1

  db:
    image: mysql:latest
    ports:
      - "3306:3306"
    volumes:
      - /path/to/volume:/var/lib/mysql
    networks:
      - network1

networks:
    network1:
        driver: bridge
```

</details>

</details>

</details>

### Projet WordPress

Le but de cet exercice sera de déployer un site WordPress avec une base de données MySQL.