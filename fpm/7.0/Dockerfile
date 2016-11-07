FROM phpdockerio/php7-fpm:latest

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install php7.0-mysql php7.0-pgsql php7.0-redis php7.0-gd php7.0-imagick php7.0-mbstring php7.0-xdebug \
    && php7.0-tidy php7.0-curl php7.0-readline \
    && php7.0-bcmath php7.0-intl php7.0-mcrypt php7.0-xml php7.0-soap php7.0-xsl php7.0-zip\
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

COPY ./php-ini-overrides.ini /etc/php/7.0/fpm/conf.d/99-overrides.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /etc/php/7.0/fpm/conf.d/20-blackfire.ini

WORKDIR "/var/www/html"