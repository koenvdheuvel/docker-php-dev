FROM php:7.0-fpm

ENV LIBICU_TAG="release-55-1"
ENV NODE_VERSION="4.2.6"

RUN set -xe && \
    buildDeps=" \
        git \
        subversion \
        # ext-bz2 \
        libbz2-dev \
        # ext-enchant \
        #libenchant-dev \
        # ext-gd \
        libpng12-dev libjpeg62-turbo-dev libfreetype6-dev zlib1g-dev libwebp-dev \
        # ext-gmp \
        libgmp3-dev \
        # ext-imap \
        #libc-client-dev libkrb5-dev \
        # ext-interbase ext-pdo_firebird
        #firebird-dev \
        # ext-intl, outdated \
        #libicu-dev \
        # ext-ldap \
        #libldap2-dev \
        # ext-mcrypt \
        libmcrypt-dev \
        # ext-pdo_dblib \
        #freetds-dev \
        # ext-oci8 ext-pdo_oci \
        #libaio-dev \
        # ext-pdo_odbc \
        #unixodbc-dev \
        # ext-pdo_pgsql \
        #libpq-dev postgresql-server-dev-all \
        # ext-pspell \
        #libpspell-dev \
        # ext-recode \
        #librecode-dev \
        # ext-soap ext-wddx ext-xmlrpc \
        libxml2-dev \
        # ext-tidy \
        libtidy-dev \
        # ext-xsl \
        libxslt1-dev \
        # pecl-memcached \
        libmemcached-dev zlib1g-dev \
        # pecl-imagick \
        libmagickwand-dev \
    " && \

    apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install $buildDeps --no-install-recommends && \

    ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/ && \

    # ext-intl
    svn export --non-interactive http://source.icu-project.org/repos/icu/icu/tags/${LIBICU_TAG} /tmp/libicu && \
    cd /tmp/libicu/source && \
    ./runConfigureICU Linux/gcc && \
    make -j$(nproc) install && \

    docker-php-ext-configure bcmath && \
    docker-php-ext-configure bz2 && \
    docker-php-ext-configure calendar && \
    docker-php-ext-configure ctype && \
    docker-php-ext-configure exif && \
    docker-php-ext-configure gettext && \
    docker-php-ext-configure gd \
        --with-freetype-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
        --with-png-dir=/usr/include \
        --with-webp-dir=/usr/include && \
    docker-php-ext-configure gmp && \
    docker-php-ext-configure intl && \
    docker-php-ext-configure json && \
    docker-php-ext-configure mcrypt && \
    docker-php-ext-configure mysqli && \
    docker-php-ext-configure opcache && \
    docker-php-ext-configure pcntl && \
    docker-php-ext-configure pdo && \
    docker-php-ext-configure pdo_mysql && \
    docker-php-ext-configure shmop && \
    docker-php-ext-configure soap && \
    docker-php-ext-configure sockets && \
    docker-php-ext-configure sysvmsg && \
    docker-php-ext-configure sysvsem && \
    docker-php-ext-configure sysvshm && \
    docker-php-ext-configure tidy && \
    docker-php-ext-configure wddx && \
    docker-php-ext-configure xsl && \
    docker-php-ext-configure zip && \

    docker-php-ext-install -j$(nproc) \
        bcmath \
        bz2 \
        calendar \
        ctype \
        exif \
        gd \
        gmp \
        gettext \
        intl \
        json \
        mcrypt \
        mysqli \
        opcache \
        pcntl \
        pdo \
        pdo_mysql \
        shmop \
        soap \
        sockets \
        sysvmsg \
        sysvsem \
        sysvshm \
        tidy \
        wddx \
        xsl \
        zip && \

    pecl install memcached && \
    pecl install imagick && \
    docker-php-ext-enable \
        memcached \
        imagick && \

    git clone --depth=1 https://github.com/longxinH/xhprof.git /tmp/xhprof && \
    cd /tmp/xhprof/extension && \
        phpize && \
        /tmp/xhprof/extension/configure --with-php-config=$(which php-config); make -j$(nproc) install && \
    docker-php-ext-enable xhprof && \

    docker-php-source delete && \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false && \
    pecl update-channels && \
    rm -rf /tmp/pear ~/.pearrc /tmp/libicu /var/lib/apt/lists/* /tmp/xhprof

# this shouldn't be included in the base image, but as the legacy implementation lives on there was no other option
RUN apt-get update && apt-get install -y \
    mysql-client \
    imagemagick \
    jpegoptim \
    librsvg2-bin \
    libimage-exiftool-perl \
    opendkim \
    opendkim-tools && \

    rm -rf /var/lib/apt/lists/*

RUN cd /opt && curl https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.gz | tar xz && \
    ln -s /opt/node-v${NODE_VERSION}-linux-x64/bin/node /usr/bin/node && \
    ln -s /opt/node-v${NODE_VERSION}-linux-x64/bin/npm /usr/bin/npm && \
    npm install -g bower npm-cache