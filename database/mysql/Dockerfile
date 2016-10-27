FROM mysql:5.7

MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ARG MAX_ALLOWED_PACKET=1073741824
ENV MAX_ALLOWED_PACKET ${MAX_ALLOWED_PACKET}

RUN sed -i -e 's/^bind-address/#bind-address/' /etc/mysql/my.cnf

RUN sed -i -e 's/^max_allowed_packet.*/max_allowed_packet = ${MAX_ALLOWED_PACKET}/' /etc/mysql/my.cnf

RUN usermod -u 1000 mysql
RUN mkdir -p /var/run/mysqld
RUN chmod -R 777 /var/run/mysqld
EXPOSE 3306
