FROM mysql:5.7
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>
ARG MAX_ALLOWED_PACKET=512M
ENV MAX_ALLOWED_PACKET ${MAX_ALLOWED_PACKET}

RUN sed -i -e 's/^bind-address/#bind-address/' /etc/mysql/my.cnf
RUN sed -ire 's/max_allowed_packet.*=.*/max_allowed_packet = 1073741824/g' /etc/mysql/my.cnf
RUN sed -i "s/^\[mysqld\]$/\[mysqld\]\nmax_allowed_packet = ${MAX_ALLOWED_PACKET}/g" /etc/mysql/my.cnf

RUN usermod -u 1000 mysql
RUN mkdir -p /var/run/mysqld
RUN chmod -R 777 /var/run/mysqld
EXPOSE 3306
