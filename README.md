# Práctica 16

## «Dockerizar» una aplicación LAMP

Para esta práctica vamos a usar el script de la práctica anterior que instala Docker y Docker Compose cambiando el archivo docker-compose.yml y creando un archivo dockerfile que instale apache junto a la web del repositorio, lo que quiere decir, crear nuestra propia imagen de docker.

En el archivo de docker-compose.yml vamos a sustituir el contenedor de Wordpress por el nuestro de apache, donde `build: ./apache` es el directorio del dockerfile:

```
apache:
build: ./apache
ports:
    - 80:80
networks:
    - frontend-network
    - backend-network
depends_on: 
    - mysql
restart: always  
```

El archivo dockerfile es el siguiente:

```
FROM ubuntu:focal

LABEL title="apache-lamp" \
  author="Alejandro Romero Rodriguez"

ENV DEBIAN_FRONTEND=noninteractive 
ENV TZ=Europe/Madrid

RUN apt-get update \
    && apt-get install -y apache2 \
    && apt-get install -y php \
    && apt-get install -y libapache2-mod-php \
    && apt-get install -y php-mysql

RUN apt install git -y \
    && cd /tmp \
    && git clone https://github.com/josejuansanchez/iaw-practica-lamp \
    && mv /tmp/iaw-practica-lamp/src/* /var/www/html/ \
    && sed -i 's/localhost/mysql/' /var/www/html/config.php \
    && rm /var/www/html/index.html

EXPOSE 80

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"] 
```

Las variables RUN son las que van a instalar tanto apache como la web desde el repositorio. Simplemente es poner comandos de bash para la instalación de los repositorios como si lo hiciesemos en una máquina normal.