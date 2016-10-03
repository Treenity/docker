FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

RUN ADD ./default.conf /etc/nginx/conf.d/

VOLUME /var/www/html

CMD ["nginx", "-g", "daemon off;"]