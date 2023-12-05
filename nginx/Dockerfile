FROM grav:1.7.43 as builder


FROM nginx:1.25-alpine

COPY etc/ /etc/nginx/
COPY docker-entrypoint.d/40-enable-cache.sh /docker-entrypoint.d/

RUN chmod 555 /docker-entrypoint.d/40-enable-cache.sh \
   && mkdir /srv/grav

COPY --from=builder /srv/grav /srv/grav

WORKDIR /srv/grav/web