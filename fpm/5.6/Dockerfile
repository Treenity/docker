FROM php:5.6-fpm
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ARG XDEBUG_ENABLE=0
ENV XDEBUG_ENABLE = ${XDEBUG_ENABLE}

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
        sudo \
        openssh-server \
        libgcc1 \
        libicu-dev \
        php5-common \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libsqlite3-dev \
        libssl-dev \
        libxml2-dev \
        libzzip-dev \
        sendmail \
        && apt-get autoremove -y && apt-get autoclean -y

COPY ./www.conf /etc/php5/fpm/pool.d/www.conf

RUN useradd docker -d /var/www/html -p null -s /bin/bash -g sudo \
    && echo "docker:docker" | chpasswd

RUN docker-php-ext-install intl mcrypt mbstring mysql mysqli pdo_mysql ftp sockets xmlrpc zip \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd && docker-php-ext-install pdo_pgsql

# Install
RUN pecl install apcu
RUN echo "extension=apcu.so" > /usr/local/etc/php/conf.d/apcu.ini

RUN touch /usr/local/etc/php/conf.d/php.ini \
    && echo "memory_limit  = 1024M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "upload_max_filesize = 1024M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "post_max_size = 1024M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "display_errors = On" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "date.timezone = UTC" >> /usr/local/etc/php/conf.d/php.ini

RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > $PHP_INI_DIR/conf.d/blackfire.ini

RUN pecl install xdebug \
    && touch $PHP_INI_DIR/conf.d/xdebug.ini \
    && echo 'zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20131226/xdebug.so' >> $PHP_INI_DIR/conf.d/xdebug.ini \
    && echo 'xdebug.remote_enable=${XDEBUG_ENABLE}' >> $PHP_INI_DIR/conf.d/xdebug.ini \
    && echo 'xdebug.profiler_enable=1' >> $PHP_INI_DIR/conf.d/xdebug.ini \
    && echo 'xdebug.remote_connect_back=1' >> $PHP_INI_DIR/conf.d/xdebug.ini
#    && echo 'xdebug.var_display_max_depth=10' >> $PHP_INI_DIR/conf.d/xdebug.ini \
#    && echo 'xdebug.cli_color=1' >> $PHP_INI_DIR/conf.d/xdebug.ini \
#    && echo 'xdebug.show_local_vars=1' >> $PHP_INI_DIR/conf.d/xdebug.ini

#RUN docker-php-ext-install opcache
#   && touch $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'zend_extension=opcache.so' >> $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'opcache.enable="1"' >> $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'opcache.use_cwd="0"' >> $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'opcache.validate_timestamps="0"' >> $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'opcache.fast_shutdown="1"' >> $PHP_INI_DIR/conf.d/opcache.ini \
#   && echo 'opcache.max_file_size="0"' >> $PHP_INI_DIR/conf.d/opcache.ini \

EXPOSE 22

CMD /etc/init.d/ssh start && php-fpm
