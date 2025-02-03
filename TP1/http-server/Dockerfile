# Utilisation de l'image Apache HTTP Server
FROM httpd:2.4

RUN echo "ServerName localhost" >> /usr/local/apache2/conf/httpd.conf

# Copie de la page web vers le dossier d’Apache
COPY index.html /usr/local/apache2/htdocs/index.html

COPY httpd.conf /usr/local/apache2/conf/httpd.conf

# Exposition du port 80 (HTTP)
EXPOSE 80
