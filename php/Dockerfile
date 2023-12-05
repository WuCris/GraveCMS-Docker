FROM php:8.2-fpm-alpine

COPY docker-entrypoint.sh /
COPY docker-entrypoint.d/ /docker-entrypoint.d/

RUN chmod 775 -R /docker-entrypoint.sh /docker-entrypoint.d
#  && mkdir -p /etc/default/template

#COPY config_files/settings.local.php /etc/default/template/
#COPY config_files/settings.php /opt/drupal/web/sites/default/

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

WORKDIR /srv/grav

ENV GRAV_VERSION 1.7.43

RUN curl -fSL "https://getgrav.org/download/core/grav-admin/${GRAV_VERSION}" -o grav.zip \
  && unzip grav.zip \
  && mv grav-admin web \
  && rm grav.zip \
  && chown -R www-data:www-data /srv/grav


ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["php-fpm"]