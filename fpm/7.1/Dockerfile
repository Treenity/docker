FROM phpdockerio/php71-fpm:latest

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install ssmtp curl openssl ca-certificates php-memcached \
    && php7.1-mysql php7.1-gd php7.1-mbstring php7.1-tidy php7.1-curl php7.1-readline \
    && php7.1-bcmath php7.1-intl php7.1-mcrypt php7.1-xml php7.1-soap php7.1-xsl php7.1-zip \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

COPY ./php-ini-overrides.ini /etc/php/7.1/fpm/conf.d/99-overrides.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /etc/php/7.1/fpm/conf.d/20-blackfire.ini

RUN sed -i -e 's/mailhub=mail/mailhub=mailhog:1025/' /etc/ssmtp/ssmtp.conf

RUN echo 'sendmail_path = "/usr/sbin/ssmtp -t"' > /etc/php/7.0/fpm/conf.d/20-mail.ini

WORKDIR "/var/www/html"