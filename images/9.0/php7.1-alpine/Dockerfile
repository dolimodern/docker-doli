FROM php:7.1-alpine

LABEL maintainer="Mathieu BRUNOT <mathieu.brunot at monogramm dot io>"

ENV DOLI_VERSION=9.0.1 \
	DOLI_AUTO_CONFIGURE=1 \
	DOLI_DB_TYPE=mysqli \
	DOLI_DB_HOST= \
	DOLI_DB_PORT=3306 \
	DOLI_DB_USER=dolibarr \
	DOLI_DB_PASSWORD='' \
	DOLI_DB_NAME=dolibarr \
	DOLI_DB_PREFIX='' \
	DOLI_DB_CHARACTER_SET=utf8 \
	DOLI_DB_COLLATION=utf8_unicode_ci \
	DOLI_DB_ROOT_LOGIN='' \
	DOLI_DB_ROOT_PASSWORD='' \
	DOLI_ADMIN_LOGIN=admin \
	DOLI_MODULES='' \
	DOLI_URL_ROOT='http://doli.orig' \
	DOLI_AUTH=dolibarr \
	DOLI_LDAP_HOST= \
	DOLI_LDAP_PORT=389 \
	DOLI_LDAP_VERSION=3 \
	DOLI_LDAP_SERVERTYPE=openldap \
	DOLI_LDAP_LOGIN_ATTRIBUTE=uid \
	DOLI_LDAP_DN='' \
	DOLI_LDAP_FILTER='' \
	DOLI_LDAP_ADMIN_LOGIN='' \
	DOLI_LDAP_ADMIN_PASS='' \
	DOLI_LDAP_DEBUG=false \
	DOLI_HTTPS=0 \
	DOLI_PROD=0 \
	DOLI_NO_CSRF_CHECK=0 \
	WWW_USER_ID=33 \
	WWW_GROUP_ID=33 \
	PHP_INI_DATE_TIMEZONE='UTC' \
	PHP_MEMORY_LIMIT=256M \
	PHP_MAX_UPLOAD=20M \
	PHP_MAX_EXECUTION_TIME=300

VOLUME /var/www/html /var/www/documents /var/www/scripts

COPY entrypoint.sh /

# Get Dolibarr
ADD https://github.com/Dolibarr/dolibarr/archive/${DOLI_VERSION}.zip /tmp/dolibarr.zip

# Install the packages we need
# Install the PHP extensions we need
# see https://wiki.dolibarr.org/index.php/Dependencies_and_external_libraries
# Prepare folders
# Install Dolibarr from tag archive
RUN set -ex; \
	apk add --no-cache \
		rsync \
		ssmtp \
	; \
	chmod 755 /entrypoint.sh; \
	apk add --no-cache --virtual .build-deps \
		$PHPIZE_DEPS \
		autoconf \
		curl-dev \
		freetype-dev \
		gcc \
		g++ \
		icu-dev \
		libjpeg-turbo-dev \
		imagemagick-dev \
		libmcrypt-dev \
		libpng-dev \
		libtool \
		libxml2-dev \
		make \
		openldap-dev \
		postgresql-dev \
		unzip \
	; \
	docker-php-ext-configure ldap; \
	docker-php-ext-configure gd --with-freetype-dir=/usr --with-png-dir=/usr --with-jpeg-dir=/usr; \
	docker-php-ext-install \
		calendar \
		gd \
		ldap \
		mbstring \
		mysqli \
		pdo \
		pdo_mysql \
		pdo_pgsql \
		pgsql \
		soap \
		zip \
	; \
    pecl install imagick; \
    docker-php-ext-enable imagick; \
	apk --purge del .build-deps; \
    mkdir -p /var/www/documents; \
    chown -R www-data:root /var/www; \
    chmod -R g=u /var/www; \
    mkdir -p /tmp/dolibarr; \
	unzip -q /tmp/dolibarr.zip -d /tmp/dolibarr; \
	rm /tmp/dolibarr.zip; \
	mkdir -p /usr/src/dolibarr; \
	cp -r /tmp/dolibarr/dolibarr-${DOLI_VERSION}/* /usr/src/dolibarr; \
	rm -rf /tmp/dolibarr; \
	chmod +x /usr/src/dolibarr/scripts/*

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm"]
