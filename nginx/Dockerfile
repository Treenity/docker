FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

COPY conf.d /etc/nginx/conf.d

WORKDIR /var/www/html

RUN usermod -u 1000 www-data