FROM mysql:5.7

RUN sed -i -e 's/^bind-address/#bind-address/' /etc/mysql/my.cnf

RUN sed -i -e 's/^max_allowed_packet.*/max_allowed_packet = 512M/' /etc/mysql/my.cnf

RUN usermod -u 1000 mysql
RUN mkdir -p /var/run/mysqld
RUN chmod -R 777 /var/run/mysqld
EXPOSE 3306
