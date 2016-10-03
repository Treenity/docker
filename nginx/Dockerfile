FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

COPY ./default.conf /etc/nginx/conf.d/

VOLUME /var/www/html

CMD ["nginx", "-g", "daemon off;"]