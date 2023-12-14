FROM php:8.2-fpm-alpine

COPY docker-entrypoint.sh /
COPY docker-entrypoint.d/ /docker-entrypoint.d/

RUN chmod 775 -R /docker-entrypoint.sh /docker-entrypoint.d

# Install the PHP extensions we need (adaptation of Docker offical Drupal source code)
RUN set -eux; \
	\
	apk add --no-cache --virtual .build-deps \
		coreutils \
		freetype-dev \
		libjpeg-turbo-dev \
		libpng-dev \
		libwebp-dev \
		libzip-dev \
	; \
	\
	docker-php-ext-configure gd \
		--with-freetype \
		--with-jpeg=/usr/include \
		--with-webp \
	; \
	\
	docker-php-ext-install -j "$(nproc)" \
		gd \
		opcache \
		zip \
	; \
	\
	runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)"; \
	apk add --no-network --virtual .drupal-phpexts-rundeps $runDeps; \
	apk del --no-network .build-deps

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
    echo 'upload_max_filesize=128M'; \
    echo 'post_max_size=128M'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

WORKDIR /srv/grav

ENV GRAV_VERSION 1.7.43

RUN curl -fSL "https://getgrav.org/download/core/grav-admin/${GRAV_VERSION}" -o grav.zip \
  && unzip grav.zip \
  && mv grav-admin web \
  && rm grav.zip \
  && chown -R www-data:www-data /srv/grav

VOLUME /srv/grav/web/user
VOLUME /srv/grav/web/backup

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["php-fpm"]