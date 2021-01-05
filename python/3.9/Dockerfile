FROM python:3.9-alpine3.12

ENV UNIT_VERSION          1.21.0-1~alpine

RUN apk update && apk upgrade

RUN set -ex; \
    apk add \
        curl \
        freetype \
        gdal \
        gettext \
        jpeg \
        lcms2 \
        libffi \
        libressl3.1-libtls \
        libwebp \
        libxml2 \
        libxslt \
        mailcap \
        openjpeg \
        pcre \
        postgresql-client \
        postgresql-libs \
        proj \
        su-exec \
        tiff \
        tini \
        yaml

RUN set -ex; \
    apk add --no-cache --virtual .build-deps \
        autoconf \
        g++ \
		gcc \
		libc-dev \
		make \
		pkgconf \
        openssl-dev \
        pcre2-dev \
        # python3-dev \
    ; \
    curl -fsSL -o /usr/src/ngunit.tar.gz https://unit.nginx.org/download/unit-1.21.0.tar.gz; \
    tar xzf /usr/src/ngunit.tar.gz -C /usr/src; \
    rm /usr/src/ngunit.tar.gz; \
    cd /usr/src/unit-1.21.0; \
    ls -la; \
    ./configure \
        --prefix=/usr/local \
        --log=/var/log/unit.log \
        --tmp=/tmp \
    ; \
    ./configure python \
        --module=python3.9 \
    ; \
    make; \
    make install; \
    apk del --no-network .build-deps; \
    cd /; \
    rm -rf /usr/src/unit-1.21.0;

RUN addgroup -g 1000 python \
    && adduser -u 1000 -G python -s /bin/sh -D python

# forward log to docker log collector
RUN ln -sf /dev/stdout /var/log/unit.log

STOPSIGNAL SIGTERM

COPY docker-entrypoint.sh /usr/local/bin/
RUN mkdir /docker-entrypoint.d/
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]

CMD ["unitd", "--no-daemon", "--control", "unix:/usr/local/control.unit.sock"]