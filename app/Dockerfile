# https://github.com/docker-library/php/blob/f016f5dc420e7d360f7381eb014ac6697e247e11/5.6/apache/Dockerfile 
FROM php:5.6-apache

# 3.6.5 - from doc
# 3.8.4 - Last
# ENV XL_VERSION 3.6.5

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        wget \
        git \
        curl \
        libssh2-1 \
        libpng16-16 \
        libmcrypt-dev \
        libpng-dev \
        libpq-dev \
        libib-util \
        bzip2 \
        imagemagick \
        libxslt1.1 \
        libc-client-dev \
        libc-client2007e \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
        && cd /tmp \
#        && export PHP_EXCEL="Excel-1.0.2-PHP5" \
        && export PHP_EXCEL="master" \
        && export XDEBUG_VERSION="2.2.7" \
        && export XL_VERSION="3.6.5" \
        && export PKG_DEPS="$PHPIZE_DEPS libldap2-dev \
            libcurl4-nss-dev libgmp-dev libmhash-dev \
            libxml2-dev libxslt1-dev \
            zlib1g-dev libssl-dev libssh2-1-dev firebird-dev \
            libbz2-dev libfreetype6-dev libjpeg62-turbo-dev \
            libmagickwand-dev libtidy-dev libkrb5-dev" \
        && apt-get install -y --no-install-recommends $PKG_DEPS \
        && wget -q http://libxl.com/download/libxl-lin-$XL_VERSION.tar.gz \
        && tar xfz libxl-lin-$XL_VERSION.tar.gz \
        && cp -p libxl-$XL_VERSION.0/lib64/libxl.so /usr/lib/libxl.so \
        && cp -r libxl-$XL_VERSION.0/include_c/ /usr/include/libxl_c \
        && mkdir php_excel \
        && wget -q https://github.com/Jan-E/php_excel/archive/$PHP_EXCEL.tar.gz \
        && tar -xf $PHP_EXCEL.tar.gz -C php_excel --strip-components=1 \
        && unset PHP_EXCEL && unset XL_VERSION && unset PKG_DEPS \
        && cd php_excel \
        && phpize \
        && ./configure \
          --with-libxml-dir=/usr/include/libxml2 \
          --with-libxl-incdir=/usr/include/libxl_c \
          --with-excel=shared \
        && make -j "$(nproc)" \
        && make install \
        && docker-php-ext-enable excel \
        && cd /tmp \
        && export PHALCON_VERSION="3.4.2" \
        && curl -LO https://github.com/phalcon/cphalcon/archive/v${PHALCON_VERSION}.tar.gz \
        && tar xzf ${PWD}/v${PHALCON_VERSION}.tar.gz \
        && docker-php-ext-install -j $(getconf _NPROCESSORS_ONLN) ${PWD}/cphalcon-${PHALCON_VERSION}/build/php5/64bits \
        && unset PHALCON_VERSION \
        && ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
        && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
        && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
        && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
        && docker-php-ext-configure mysql --with-mysql=mysqlnd \
        && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
        && docker-php-ext-configure gd -with-freetype-dir=/usr/include/ -with-jpeg-dir=/usr/include/ \
        && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
        && docker-php-ext-install \
            curl \
            ldap \
            soap \
            sysvmsg \
            sysvsem \
            sysvshm \
            mcrypt \
            gmp \
            bcmath \
            zip \
            tidy \
            bz2 \
            exif \
            gd \
            pcntl \
            sockets \
            shmop \
            wddx \
            gettext \
            pdo_mysql \
            pdo_pgsql \
            pdo_firebird \
            mysqli \
            mysql \
            pgsql \
            interbase \
            imap \
            xmlrpc \
            xsl \
        && pecl install imagick \
        && docker-php-ext-enable imagick \
        && pecl install xdebug-$XDEBUG_VERSION \
        && docker-php-ext-enable xdebug && unset XDEBUG_VERSION \
        && pecl install igbinary-2.0.1 \
        && docker-php-ext-enable igbinary \
        && pecl install ssh2 \
        && docker-php-ext-enable ssh2 \
        && pecl install -o -f redis \
        && docker-php-ext-enable redis \
        && apt-get purge -y gcc cpp make zlib1g-dev linux-libc-dev libxml2-dev \
            libicu-dev libc6-dev libc-dev-bin $PKG_DEPS \
        && apt-get autoremove -y \
        && apt-get clean \
        && rm -rf /tmp/* /var/lib/apt/lists/*

RUN a2enmod rewrite remoteip \
    && {\
         echo RemoteIPHeader X-Real-IP ;\
         echo RemoteIPTrustedProxy 10.0.0.0/8 ;\
         echo RemoteIPTrustedProxy 172.16.0.0/12 ;\
         echo RemoteIPTrustedProxy 192.168.0.0/16 ;\
       } > /etc/apache2/conf-available/remoteip.conf \
    && a2enconf remoteip

COPY php.ini /usr/local/etc/php/conf.d/99-setting.ini

COPY excel.ini /usr/local/etc/php/conf.d/99-excel.ini

RUN mkdir /var/www/projects

WORKDIR /var/www/projects

EXPOSE 80

CMD ["apache2-foreground"]
