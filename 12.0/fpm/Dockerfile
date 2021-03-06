FROM resin/rpi-raspbian:latest

RUN apt-get update && apt-get install -y \
	rsync \
	bzip2 \
	libcurl4-openssl-dev \
	libfreetype6-dev \
	libicu-dev \
	libjpeg-dev \
	libldap2-dev \
	libmcrypt-dev \
	libmemcached-dev \
        libpng12-dev \
        #libpng-dev \
	libpq-dev \
	libxml2-dev \
	curl \
	cron

RUN echo "deb http://mirrordirector.raspbian.org/raspbian/ stretch main contrib non-free rpi" >> /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
	php7.0 \
	php7.0-fpm \
	php7.0-mysql \
	php7.0-curl \
	php7.0-gd \
	php7.0-apcu \
        php7.0-xml \
        php7.0-zip \
        php7.0-mbstring \
	&& rm -rf /var/lib/apt/lists/*

# https://doc.owncloud.org/server/8.1/admin_manual/installation/source_installation.html#prerequisites
#RUN docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
#	&& docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
#	&& docker-php-ext-install exif gd intl ldap mbstring mcrypt mysql opcache pdo_mysql pdo_pgsql pgsql zip

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
#RUN { \
#		echo 'opcache.memory_consumption=128'; \
#		echo 'opcache.interned_strings_buffer=8'; \
#		echo 'opcache.max_accelerated_files=4000'; \
#		echo 'opcache.revalidate_freq=60'; \
#		echo 'opcache.fast_shutdown=1'; \
#		echo 'opcache.enable_cli=1'; \
#	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# PECL extensions
#RUN set -ex \
#	&& pecl install APCu-4.0.10 \
#	&& pecl install memcached-2.2.0 \
#	&& pecl install redis-2.2.8 \
#	&& docker-php-ext-enable apcu memcached redis

ENV NEXTCLOUD_VERSION 12.0.3

RUN mkdir -p /var/www/html
RUN chown -R www-data:root /var/www/html && \
    chmod -R g=u /var/www/html
VOLUME /var/www/html

RUN curl -k -fsSL -o nextcloud.tar.bz2 \
    "https://download.nextcloud.com/server/releases/nextcloud-${NEXTCLOUD_VERSION}.tar.bz2" \
 && curl -k -fsSL -o nextcloud.tar.bz2.asc \
    "https://download.nextcloud.com/server/releases/nextcloud-${NEXTCLOUD_VERSION}.tar.bz2.asc" \
 && export GNUPGHOME="$(mktemp -d)" \
# gpg key from https://nextcloud.com/nextcloud.asc
 && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 28806A878AE423A28372792ED75899B9A724937A \
 && gpg --batch --verify nextcloud.tar.bz2.asc nextcloud.tar.bz2 \
 && rm -r "$GNUPGHOME" nextcloud.tar.bz2.asc \
 && tar -xjf nextcloud.tar.bz2 -C /usr/src/ \
 && rm nextcloud.tar.bz2 \
 && rm -rf /usr/src/nextcloud/updater \
 && mkdir -p /usr/src/nextcloud/data \
 && mkdir -p /usr/src/nextcloud/custom_apps \
 && chmod +x /usr/src/nextcloud/occ

ADD Sapi.php /usr/src/nextcloud/3rdparty/sabre/http/lib/Sapi.php

RUN service php7.0-fpm start

COPY docker-entrypoint.sh /entrypoint.sh
COPY config/* /usr/src/nextcloud/config/

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php7.0-fpm", "-F"]
