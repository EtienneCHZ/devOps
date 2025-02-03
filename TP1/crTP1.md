## Etienne CHAIZE 4IRC
# Compte rendu TP1 DOCKER

### Q1: Why should we run the container with a flag -e to give the environment variables?
On utilise le flag -e pour passer des variables d’environnement à un conteneur Docker afin de rendre celui-ci plus configurable et flexible.
Le flag -e est donc essentiel pour séparer la configuration du code, renforcer la sécurité, et simplifier le déploiement. C’est une bonne pratique en DevOps !

### Q2: Why do we need a volume to be attached to our postgres container?
Un volume est nécessaire pour assurer la persistance des données. Par défaut, lorsqu'un conteneur est supprimé, toutes les données stockées à l’intérieur sont perdues.

### Q3: Document your database container essentials: commands and Dockerfile.
```docker
docker run --name my-postgres --net=app-network -v /data:/var/lib/postgresql/data -d tp1
```

```docker
docker exec -it my-postgres psql -U usr -d db -c "INSERT INTO students (department_id, first_name, last_name) VALUES (1, 'John', 'Doe');"
```
```docker
docker exec -it my-postgres psql -U usr -d db -c "SELECT * FROM students;"
```

### Q4: 1-4 Why do we need a multistage build? And explain each step of this dockerfile.

- Sans multistage build : 
    - L’image contiendrait Maven, le JDK et toutes les dépendances, ce qui la rend lourde et inutilement complexe.

- Avec multistage build :
    - Compilation avec Maven et JDK (image lourde).
    - Exécution avec un simple JRE (image plus légère).

Le multistage est donc une bonn pratique qui permet un gain en performance, sécurité et stockage !


Ce Dockerfile est structuré en deux étapes :

#### 1ère étape : Build (Compilation)

```
FROM maven:3.9.9-amazoncorretto-21 AS myapp-build
```
On utilise une image Maven avec JDK pour compiler le projet.
On nomme cette étape myapp-build pour y référencer plus tard.
```
ENV MYAPP_HOME=/opt/myapp 
WORKDIR $MYAPP_HOME
```
On définit une variable d’environnement MYAPP_HOME pour organiser les fichiers dans un répertoire /opt/myapp.
On se place dans ce répertoire avec WORKDIR.
```
COPY pom.xml .
COPY src ./src
```
On copie les fichiers du projet dans l’image (pom.xml et le dossier src contenant le code Java).
```
RUN mvn package -DskipTests
```
On lance Maven pour compiler le projet et générer un fichier .jar dans target/, en désactivant les tests (-DskipTests).
#### 2ème étape : Run (Exécution)
```
FROM amazoncorretto:21
```
On utilise une image plus légère contenant uniquement le JRE (amazoncorretto:21), car la compilation n’est plus nécessaire.
```
ENV MYAPP_HOME=/opt/myapp 
WORKDIR $MYAPP_HOME
```
On définit le même répertoire de travail.
```
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
```
On récupère uniquement le .jar généré à l’étape précédente depuis myapp-build et le copie dans l’image finale.
```
ENTRYPOINT ["java", "-jar", "myapp.jar"]
```
On définit la commande d’exécution du conteneur :
Quand le conteneur démarre, il exécute java -jar myapp.jar.

### Q5: 1-5 Why do we need a reverse proxy?

Un reverse proxy améliore la sécurité, la gestion du trafic, l’optimisation des performances et la gestion des connexions client/serveur:

- Sécurité
Cache les adresses IP des serveurs backend, rendant l'infrastructure plus difficile à attaquer.
Peut filtrer les requêtes malveillantes et bloquer certains accès non autorisés.

- Gestion du trafic et équilibrage de charge
Permet de répartir les requêtes entre plusieurs serveurs backend pour éviter une surcharge.
Améliore la scalabilité et assure une meilleure disponibilité du service.

- Unification des accès 
Permet d'avoir un seul point d'entrée pour différents services backend (ex : API, base de données, application front-end).
Simplifie la gestion des domaines et sous-domaines.

- Optimisation des performances 
Peut implémenter un cache pour stocker des réponses et réduire la charge des serveurs backend.
Comprime les réponses HTTP pour un chargement plus rapide des pages.

- Gestion des certificats SSL 
Permet d'ajouter facilement un certificat SSL et de gérer le HTTPS sans avoir à configurer chaque backend.

### Q6: 1-6 Why is docker-compose so important?
Docker Compose est essentiel pour plusieurs raisons :

- Orchestration multi-conteneurs : Permet de gérer plusieurs services (base de données, backend, reverse proxy, etc.) dans un seul fichier.
- Facilité de déploiement : Une seule commande (docker-compose up -d) permet de tout lancer.
- Gestion des dépendances : depends_on garantit que les conteneurs démarrent dans le bon ordre.
- Isolation des environnements : Crée un réseau spécifique (my-network), évitant ainsi les conflits avec d'autres services.
- Portabilité : Un simple docker-compose.yml permet de recréer un environnement identique sur n'importe quelle machine.

### Q7: 1-7 Document docker-compose most important commands.

```docker-compose up -d```	Démarre tous les services en arrière-plan.
```docker-compose up --build```	Reconstruit les images avant de démarrer les services.
```docker-compose down```	Arrête et supprime les conteneurs, les réseaux et les volumes associés.
```docker-compose ps```	Affiche la liste des conteneurs en cours d'exécution.
```docker-compose logs -f <service>```	Affiche les logs en temps réel d’un service spécifique.
```docker-compose exec <service> <commande>```	Exécute une commande à l'intérieur d’un conteneur en cours d’exécution.
```docker-compose restart <service>```	Redémarre un service spécifique.
```docker-compose stop```	Arrête tous les conteneurs sans les supprimer.


### Q9 1-9 Document your publication commands and published images in dockerhub.

1) Connexion à Docker Hub
Avant de publier une image, il faut se connecter à Docker Hub :

```
docker login
```

2) Création des tags pour les images
Il faut donner un nom clair et une version à chaque image avant de la publier.

```
docker tag my-database USERNAME/my-database:1.0
docker tag my-backend USERNAME/my-backend:1.0
docker tag my-httpd USERNAME/my-httpd:1.0
```
Remplace USERNAME par ton nom d’utilisateur Docker Hub.

3) Pousser les images vers Docker Hub
On envoie chaque image vers Docker Hub avec la commande docker push :

```
docker push USERNAME/my-database:1.0
docker push USERNAME/my-backend:1.0
docker push USERNAME/my-httpd:1.0
```
Après cela, tu devrais voir tes images publiées dans ton compte Docker Hub.


### Q10 1-10 Why do we put our images into an online repo?

- Partage facile : Une fois une image publiée, n'importe quel membre de l'équipe peut la récupérer sur une autre machine en exécutant un simple docker pull.

- Déploiement rapide : En utilisant un registre d’images, on peut automatiser le déploiement sur des serveurs en production sans avoir à reconstruire les images localement.

- Versioning & rollback : En publiant plusieurs versions (1.0, 1.1, latest...), on peut revenir en arrière en cas de problème.

- CI/CD : Les pipelines d’intégration continue peuvent directement récupérer l’image depuis le registre, ce qui permet d’automatiser les tests et les mises en production.

- Sauvegarde centralisée : On évite de perdre des images en les stockant dans un dépôt en ligne plutôt que sur une seule machine.

En entreprise, les images Docker sont souvent hébergées sur des registres privés comme Amazon ECR, Google Container Registry, GitHub Container Registry ou un registre auto-hébergé avec Harbor. Cela garantit plus de sécurité et un meilleur contrôle sur les images.