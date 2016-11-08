FROM phpdockerio/nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

COPY ./nginx.conf /etc/nginx/nginx.conf

VOLUME ['/var/www/html', '/etc/nginx/conf.d/']

WORKDIR ['/var/www/html']
