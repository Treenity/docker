FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

COPY conf.d /etc/nginx/conf.d

COPY conf.d/proxy.conf /etc/nginx/conf.d/proxy.conf
COPY conf.d/gzip.conf /etc/nginx/conf.d/gzip.conf
COPY conf.d/upstream.conf /etc/nginx/conf.d/upstream.conf
COPY conf.d/default.conf /etc/nginx/conf.d/default.conf

WORKDIR /var/www/html
