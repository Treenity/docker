FROM nginx:latest
MAINTAINER Ronan Pozzi <contact@treenity-web.fr>

ADD ./default.conf /etc/nginx/conf.d/default.conf

COPY ./php.ini /usr/local/etc/php/php.ini

RUN docker-php-ext-configure gd \
		--with-freetype-dir=/usr/lib/x86_64-linux-gnu/ \
		--with-jpeg-dir=/usr/lib/x86_64-linux-gnu/ \
		--with-xpm-dir=/usr/lib/x86_64-linux-gnu/ \
		--with-vpx-dir=/usr/lib/x86_64-linux-gnu/ \
	&& \

# Install some extra PHP Extension

	docker-php-ext-install \
		bcmath \
		bz2 \
		exif \
		ftp \
		gd \
		gettext \
		mbstring \
		mcrypt \
		mysql \
		pdo \
		mysqli \
		opcache \
		pdo_mysql \
		shmop \
		sockets \
		sysvmsg \
		sysvsem \
		sysvshm \
		zip \
	&& \

# Some extensions need to be installed via PECL

	pecl install apcu-4.0.8 memcached && \
	echo 'extension = apcu.so' > /usr/local/etc/php/conf.d/apcu.ini && \
	echo 'extension = memcached.so' > /usr/local/etc/php/conf.d/memcached.ini

# php5 apache2 stuff

COPY ./php.ini /usr/local/etc/php/php.ini

RUN touch /usr/local/etc/php/conf.d/php.ini \
    && echo "memory_limit  = 128M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "upload_max_filesize = 64M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "post_max_size = 64M" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "display_errors = On" >> /usr/local/etc/php/conf.d/php.ini \
    && echo "date.timezone = Europe/Paris" >> /usr/local/etc/php/conf.d/php.ini

# xdebug
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

EXPOSE 80

VOLUME /usr/share/nginx/html

WORKDIR /usr/share/nginx/html