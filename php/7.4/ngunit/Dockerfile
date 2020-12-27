FROM syaiful6/php:7.4-alpine3.11

ENV UNIT_VERSION          1.18.0-1~alpine

RUN set -eux; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        openssl-dev \
        curl-dev \
    ; \
    curl -fsSL -o /usr/src/ngunit.tar.gz https://unit.nginx.org/download/unit-1.18.0.tar.gz; \
    tar xzf /usr/src/ngunit.tar.gz -C /usr/src; \
    rm /usr/src/ngunit.tar.gz; \
    cd /usr/src/unit-1.18.0; \
    ls -la; \
    ./configure \
        --prefix=/usr/local \
        --log=/var/log/unit.log \
        --tmp=/tmp \
    ; \
    ./configure php \
        --module=php7.4 \
    ; \
    make; \
    make install; \
    apk del --no-network .build-deps; \
    cd /; \
    rm -rf /usr/src/unit-1.18.0;

RUN apk add --no-cache --virtual .deps shadow \
    && usermod -u 1000 www-data \
    && groupmod -g 1000 www-data \
    && apk del --no-network .deps

# forward log to docker log collector
RUN ln -sf /dev/stdout /var/log/unit.log

STOPSIGNAL SIGTERM

COPY docker-entrypoint.sh /usr/local/bin/
RUN mkdir /docker-entrypoint.d/
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]

CMD ["unitd", "--no-daemon", "--control", "unix:/usr/local/control.unit.sock"]