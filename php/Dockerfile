FROM php:8.2-fpm-alpine

COPY docker-entrypoint.sh /
COPY docker-entrypoint.d/ /docker-entrypoint.d/

RUN chmod 775 -R /docker-entrypoint.sh /docker-entrypoint.d
#  && mkdir -p /etc/default/template

#COPY config_files/settings.local.php /etc/default/template/
#COPY config_files/settings.php /opt/drupal/web/sites/default/

WORKDIR /srv/grav

ENV GRAV_VERSION 1.7.43

RUN curl -fSL "https://getgrav.org/download/core/grav-admin/${GRAV_VERSION}" -o grav.zip \
  && unzip grav.zip \
  && mv grav-admin web \
  && rm grav.zip \
  && chown -R www-data:www-data /srv/grav


ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["php-fpm"]