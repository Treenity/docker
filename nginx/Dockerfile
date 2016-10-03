FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ADD ./default.conf /etc/nginx/conf.d/default.conf

DD conf.d /etc/nginx/conf.d

EXPOSE 80

VOLUME /var/www

WORKDIR /var/www