FROM syaiful6/php:7.4-alpine3.11

ENV UNIT_VERSION          1.16.0-1~alpine

RUN set -eux; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        openssl-dev \
        curl-dev \
    ; \
    curl -fsSL -o /usr/src/ngunit.tar.gz https://unit.nginx.org/download/unit-1.16.0.tar.gz; \
    tar xzf /usr/src/ngunit.tar.gz -C /usr/src; \
    cd /usr/src/unit-1.16.0; \
    ls -la; \
    ./configure \
        --prefix=/usr/local \
        --log=/var/log/unit.log \
    ; \
    ./configure php \
        --module=php7.4 \
    ; \
    make; \
    make install; \
    apk del --no-network .build-deps;

# forward log to docker log collector
RUN ln -sf /dev/stdout /var/log/unit.log

STOPSIGNAL SIGTERM

COPY docker-entrypoint.sh /usr/local/bin/
RUN mkdir /docker-entrypoint.d/
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]

CMD ["unitd", "--no-daemon", "--control", "unix:/usr/local/control.unit.sock"]