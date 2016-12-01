FROM php:5.6-fpm

MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ENV PHP_MEMORY_LIMIT=512M \
    MAX_UPLOAD=50M \
    PHP_MAX_FILE_UPLOAD=200 \
    PHP_MAX_POST=100M \
    TIMEZONE=UTC

RUN DEBIAN_FRONTEND=noninteractive apt-get update -q \
    && DEBIAN_FRONTEND=noninteractive apt-get dist-upgrade -y \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \
        libcurl4-nss-dev \
        php5-xdebug \
        php5-xhprof \
        php5-memcached \
        memcached \
        php5-memcache \
        ssmtp \
    && docker-php-ext-install iconv mcrypt \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install curl \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install json \
    && docker-php-ext-install intl \
    && docker-php-ext-install opcache \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN cp /usr/lib/php5/20131226/* /usr/local/lib/php/extensions/no-debug-non-zts-20131226/
RUN cp /etc/php5/mods-available/* /usr/local/etc/php/conf.d

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install BlackFire agent
RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > $PHP_INI_DIR/conf.d/blackfire.ini

# Override ssmtp to mailhog
RUN sed -i -e 's/mailhub=mail/mailhub=mailhog:1025/' /etc/ssmtp/ssmtp.conf

# Tune up PHP-FPM
RUN sed -i -e "s|;catch_workers_output\s*=.*|catch_workers_output = yes|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|^pm.max_children =.*|pm.max_children = 9|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|^pm.start_servers =.*|pm.start_servers = 3|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|^pm.min_spare_servers =.*|pm.min_spare_servers = 2|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|^pm.max_spare_servers =.*|pm.max_spare_servers = 4|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|;pm.max_requests =.*|pm.max_requests = 200|g" $PHP_INI_DIR/pool.d/www.conf && \
    sed -i -e "s|;security.limit_extensions =.*|security.limit_extensions = .php|g" $PHP_INI_DIR/pool.d/www.conf

# Tune up PHP.ini
RUN sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" $PHP_INI_DIR/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|" $PHP_INI_DIR/php.ini && \
    sed -i "s|upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|" $PHP_INI_DIR/php.ini && \
    sed -i "s|max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|" $PHP_INI_DIR/php.ini && \
    sed -i "s|post_max_size =.*|max_file_uploads = ${PHP_MAX_POST}|" $PHP_INI_DIR/php.ini && \
    sed -i 's|short_open_tag =.*|short_open_tag = On|' $PHP_INI_DIR/php.ini && \
    sed -i 's|error_reporting =.*|error_reporting = -1|' $PHP_INI_DIR/php.ini && \
    sed -i 's|display_errors =.*|display_errors = On|' $PHP_INI_DIR/php.ini && \
    sed -i 's|display_startup_errors =.*|display_startup_errors = On|' $PHP_INI_DIR/php.ini && \
    sed -i -re 's|^(;?)(session.save_path) =.*|\2 = "/tmp"|g' $PHP_INI_DIR/php.ini

# Copy override
COPY ./php-ini-overrides.ini $PHP_INI_DIR/conf.d/99-overrides.ini


WORKDIR "/var/www/html"