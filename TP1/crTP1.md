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



