FROM phpdockerio/php71-fpm:latest

MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ENV PHP_MEMORY_LIMIT=512M \
    MAX_UPLOAD=512M \
    PHP_MAX_FILE_UPLOAD=200 \
    PHP_MAX_POST=100M \
    TIMEZONE=UTC

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install ssmtp curl openssl ca-certificates php-memcached \
    && php7.1-mysql php7.1-gd php7.1-mbstring php7.1-tidy php7.1-curl php7.1-readline \
    && php7.1-bcmath php7.1-intl php7.1-mcrypt php7.1-xml php7.1-soap php7.1-xsl php7.1-zip \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install BlackFire agent
RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /etc/php/7.1/fpm/conf.d/20-blackfire.ini

# Override ssmtp to mailhog
RUN sed -i -e 's/mailhub=mail/mailhub=mailhog:1025/' /etc/ssmtp/ssmtp.conf

# Tune up PHP-FPM
RUN sed -i -e "s|;catch_workers_output\s*=.*|catch_workers_output = yes|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|^pm.max_children =.*|pm.max_children = 9|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|^pm.start_servers =.*|pm.start_servers = 3|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|^pm.min_spare_servers =.*|pm.min_spare_servers = 2|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|^pm.max_spare_servers =.*|pm.max_spare_servers = 4|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|;pm.max_requests =.*|pm.max_requests = 200|g" /etc/php/7.1/fpm/pool.d/www.conf && \
    sed -i -e "s|;security.limit_extensions =.*|security.limit_extensions = .php|g" /etc/php/7.1/fpm/pool.d/www.conf

# Tune up PHP.ini
RUN sed -i "s|;date.timezone =.*|date.timezone = ${TIMEZONE}|" /etc/php/7.1/fpm/php.ini && \
    sed -i "s|memory_limit =.*|memory_limit = ${PHP_MEMORY_LIMIT}|" /etc/php/7.1/fpm/php.ini && \
    sed -i "s|upload_max_filesize =.*|upload_max_filesize = ${MAX_UPLOAD}|" /etc/php/7.1/fpm/php.ini && \
    sed -i "s|max_file_uploads =.*|max_file_uploads = ${PHP_MAX_FILE_UPLOAD}|" /etc/php/7.1/fpm/php.ini && \
    sed -i "s|post_max_size =.*|max_file_uploads = ${PHP_MAX_POST}|" /etc/php/7.1/fpm/php.ini && \
    sed -i 's|short_open_tag =.*|short_open_tag = On|' /etc/php/7.1/fpm/php.ini && \
    sed -i 's|error_reporting =.*|error_reporting = -1|' /etc/php/7.1/fpm/php.ini && \
    sed -i 's|display_errors =.*|display_errors = On|' /etc/php/7.1/fpm/php.ini && \
    sed -i 's|display_startup_errors =.*|display_startup_errors = On|' /etc/php/7.1/fpm/php.ini && \
    sed -i -re 's|^(;?)(session.save_path) =.*|\2 = "/tmp"|g' /etc/php/7.1/fpm/php.ini

# Copy override
COPY ./php-ini-overrides.ini /etc/php/7.1/fpm/conf.d/99-overrides.ini

WORKDIR "/var/www/html"