FROM alpine:3

ARG VERSION=1.18

LABEL   maintainer="@iiriix" \
        org.label-schema.description="Runs nginx in front of a wordpress service in docker swarm cluster" \
        org.label-schema.name="nginx-wp" \
        org.label-schema.url="https://github.com/iiriix/docker-swarm-wordpress" \
        org.label-schema.vcs-url="https://github.com/iiriix/docker-swarm-wordpress" \
        org.label-schema.schema-version="1.0"

RUN set -ex && \
    #
    # Install shadow to create nginx user & group
    # with the same uid & gid as in wordpress image
    apk add --no-cache --virtual .build-deps shadow && \
    groupadd -g 82 nginx && \
    useradd -u 82 -g 82 -c nginx -m -d /var/lib/nginx -s /sbin/nologin nginx && \
    #
    # Install nginx and cache purge module
    apk add --no-cache nginx nginx-mod-http-cache-purge && \
    #
    # Cleanup
    apk del .build-deps && \
    rm -rf /var/www/localhost/ && \
    #
    # Create dir for fastcgi_cache
    mkdir -p /var/cache/nginx && \
    chown -R nginx.nginx /var/cache/nginx && \
    #
    # Forward request and error logs to docker log collector
    ln -sf /dev/stdout /var/log/nginx/access.log && \
    ln -sf /dev/stderr /var/log/nginx/error.log

ADD nginx_conf /etc/nginx/

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
