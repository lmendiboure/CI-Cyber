# Séance 12: Mise en pratique de différents concepts de sécurité - La Blockchain
-------------

L’objectif du jour est de découvrir la Blockchain et les différents éléments qui peuvent être mis en pratique grâce à cette technologie.

Pour cet exercice pratique, nous allons utiliser Geth (Go Ethereum), qui est l'implémentation d'Ethereum avec le langage GO (https://geth.ethereum.org/). Il existe d'autres implémentations d'Ethereum, mais la version Go reste la plus répandue et la plus facile à apprendre.

L'autre outil important ici est le compilateur Solidity (https://solidity.readthedocs.io/en/v0.4.21/), solc. Il nous permettra de compiler les fichiers Solidity utilisés pour implémenter les smart contracts (contrats intelligents) avec Ethereum.

Les conteneurs Docker seront utilisés pour toutes ces applications pratiques. Cela présente plusieurs avantages : 1) la possibilité de déployer plusieurs nœuds avec des fonctionnalités différentes sur la même machine ; 2) la possibilité de tester rapidement différents scénarios ; et 3) l'absence de nécessité d'installer des outils autres que Docker (ET Docker Compose) sur la machine utilisée.

Il convient de noter que nous utiliserons un environnement de test, et donc une blockchain privée dans laquelle nous pouvons créer des utilisateurs, allouer des éthers, effectuer des transactions, développer et tester des contrats intelligents gratuitement et rapidement. Il existe également un certain nombre de réseaux de test Ethereum (Testnet), mais ceux-ci sont principalement destinés à tester des smart contracts et non à expérimenter des éléments de l'architecture de la blockchain.

**Important:** A la fin de la session, veuillez m'envoyer un court rapport par groupe de 2 étudiants répondant aux questions posées dans ce TP (romain.dulout@univ-eiffel.fr).

## Prérequis (15 minutes max)

**Remarque** Cette partie peut être sautée par les personnes ayant des connaissances sur les principes et commandes de base de docker (l'idée de ce TP n'étant pas de vous former à docker et au devops de manière générale mais de vous introduire quelques concepts de la blockchain). 

-> Qu'est-ce que docker ? 

Docker est une plateforme de conteneurisation open-source qui permet d'emballer des applications et leurs dépendances dans des conteneurs légers, portables et isolés. En d'autres termes, il s'agit de machines virtuelles beaucoup plus légères, que l'on appelle des conteneurs lors de leurs phases d'exécution. Chaque conteneur docker partage le même noyau du système d'exploitation, là où les VMs émulent un système d'exploitation complet (y compris la mémoire, le stockage et le processeur).

Les conteneurs Docker sont autonomes, ce qui signifie qu'ils incluent tout ce dont une application a besoin pour s'exécuter, y compris le code, les bibliothèques et les configurations.

D'un point de vue technique, Docker offre de nombreux avantages en simplifiant notamment le déploiement et la gestion des applications, car les conteneurs peuvent être exécutés de manière cohérente sur n'importe quelle infrastructure compatible Docker (serveur, hôtes MAC OS, windows, linux) offrant une grande portabilité. 

Enfin, Il affiche une efficacité et une flexibilité accrues, car les conteneurs peuvent être rapidement déployés, mis à jour et réduits, tout en garantissant l'isolation des applications.

Il est aujourd'hui l'une des technologies centrales de l'approche DevOps (https://about.gitlab.com/topics/devops/), que tout nouveau développeur sur le marché se doit de connaître. 

### Installation de Docker

- **Etape 1:** Installez Docker sur votre machine locale.

 Suivez le guide d'installation officiel de votre système d'exploitation (https://docs.docker.com/engine/install/ubuntu/).

  ```shell
  # Example for Linux (Ubuntu)
  sudo apt-get update
  sudo apt-get install docker-ce

```

Pour vérifier que Docker est correctement installé sur votre machine, vous pouvez utiliser la commande suivante :

```shell
docker run hello-world
```
Dans cette ligne de commande :
  1. `docker` indique à votre système d'exploitation que vous utilisez le programme docker
  2. `run` est l'une des différentes sous-commandes proposées par docker pour créer et exécuter un conteneur docker.
  3. `hello-world` est utilisé pour indiquer à docker que vous utilisez une image spécifique qui sera chargée dans le conteneur.

### Télécharger (pull) et exécuter (run) une image Docker 

- **Étape 1:** Téléchargez (pull) une image Docker.

  Téléchargez l'image Docker "nginx".

   ```shell
  docker pull nginx
  ```

  - **Étape 2:** Lancez un conteneur NGINX.

  Démarrez un conteneur NGINX en mode détaché.

  ```shell
  docker run -d -p 8080:80 nginx
  ```

- **Étape 3:** Accédez au serveur web NGINX.

  Ouvrez un navigateur web et naviguez jusqu'à `http://localhost:8080`.

Vous venez de lancer votre premier environnement conteneurisé, à partir de l'image NGINX (téléchargée depuis dockerhub, librairie en ligne qui contient l'ensemble des images docker).

Nous allons voir maintenant comment créer vos propres images Docker.

### Construire son image docker

- **Étape 1:** Créez un répertoire pour votre projet Docker.
- **Étape 2:** Créez un fichier Dockerfile avec les instructions pour construire un serveur web personnalisé en utilisant NGINX. (exemple de fichier index.html dans ce dossier)

  ```Dockerfile
  # Utilisez l'image de base officielle de NGINX
  FROM nginx

  # Copiez vos fichiers HTML personnalisés à la racine du document NGINX
  COPY custom-html-files /usr/share/nginx/html
  ```

- **Étape 3:** Construisez l'image Docker personnalisée (qui s'appelera my-web-server) là où vous avez votre Dockerfile.

  ```shell
  docker build -t my-web-server .
  ```

- **Étape 4:** Exécutez un conteneur à partir de votre image personnalisée.

  ```shell
  docker run -d -p 8081:80 my-web-server
  ```

- **Étape 5:** Accédez au serveur web personnalisé.

  Ouvrez un navigateur web et naviguez jusqu'à `http://localhost:8081`.

Vous venez de créer votre image NGINX custom, et lancer ce nouvel environnement conteneurisé.
Nous allons à présent lancer des environnements plus complexes à l'aide de l'orchestrateur de conteneurs docker : docker-compose.

### Orchestration de plusieurs conteneurs docker

- **Étape 1:** Installez Docker Compose.

  Suivez le guide d'installation officiel de Docker Compose sur votre système d'exploitation (ou vous pouvez essayer d'exécuter le script situé dans ce dossier). 

- **Étape 2:** Créez un répertoire de projet Docker Compose.

  Créez un répertoire nommé `my-docker-app` pour votre projet Docker Compose.

- **Étape 3:** Définissez un fichier YAML pour Docker Compose.

Créez un fichier nommé `docker-compose.yml` dans le répertoire du projet et définissez-y des services (qui sont nos conteneurs). Par exemple, créez des services pour une application web et une base de données (par exemple, WordPress et MySQL).


  ```yaml
  version: '3'
  services:
    wordpress:
      image: wordpress:latest
      ports:
        - "8080:80"
      environment:
        WORDPRESS_DB_HOST: db
        WORDPRESS_DB_USER: exampleuser
        WORDPRESS_DB_PASSWORD: examplepassword
        WORDPRESS_DB_NAME: exampledb
    db:
      image: mysql:5.7
      environment:
        MYSQL_ROOT_PASSWORD: examplepassword
        MYSQL_USER: exampleuser
        MYSQL_PASSWORD: examplepassword
        MYSQL_DATABASE: exampledb
  ```

- **Étape 4:** Démarrez les services Docker Compose.

  Exécutez la commande suivante dans le répertoire du projet pour démarrer les services définis en mode détaché.

  ```Shell
  docker-compose up -d
  ```

- **Étape 5:** Accédez à l'application.

  Ouvrez un navigateur web et naviguez vers `http://localhost:8080`.

Vous venez de lancer un environnement complexe dans lequel plusieurs services (conteneurs) interdépendant sont initialisés puis exécutés. 
Chaque conteneur est décrit dans la partie service du fichier docker-compose.yml. Vous pouvez modifier la valeur de chaque clé pour customiser les différents services décrits dans ce fichier.

Vous n'aurez pas dans la suite du TP le besoin de connaître l'ensemble de ces commandes, retenez simplement la philosophie.


## A. Déploiement d'un réseau Blockchain

Le dossier dans lequel se trouve cet énoncé contient divers fichiers qui peuvent être utilisés pour lancer un réseau Blockchain, y compris 1) deux DockerFiles et 2) un fichier Docker-Compose.

Utilisez la commande `docker-compose up -d` pour lancer le réseau Blockchain.
