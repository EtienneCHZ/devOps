FROM postgres:14.1-alpine

# Copie des scripts d'initialisation dans le répertoire spécial
COPY /init-scripts /docker-entrypoint-initdb.d/

# Définition des variables d’environnement (optionnel, mieux vaut les passer via -e)
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd
