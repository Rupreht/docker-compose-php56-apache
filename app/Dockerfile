# https://github.com/docker-library/php/blob/f016f5dc420e7d360f7381eb014ac6697e247e11/5.6/apache/Dockerfile 
FROM php:5.6-apache

# 3.6.5 - from doc
# 3.8.4 - Last
ENV XL_VERSION 3.6.5
ENV PHP_EXCEL Excel-1.0.2-PHP5
ENV XDEBUG_VERSION 2.2.7

ENV PKG_DEPS $PHPIZE_DEPS libxml2-dev libldap2-dev \
    libcurl4-nss-dev libgmp-dev libmhash-dev \
    zlib1g-dev libssl-dev libssh2-1-dev

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        wget \
        git \
        curl \
        re2c \
        libssh2-1 \
        file \
        libpng16-16 \
        libmcrypt-dev \
        libpng-dev \
        libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
        && cd /tmp \
        && apt-get install -y --no-install-recommends $PKG_DEPS \
        && wget -q http://libxl.com/download/libxl-lin-$XL_VERSION.tar.gz \
        && tar xfz libxl-lin-$XL_VERSION.tar.gz \
        && cp -p libxl-$XL_VERSION.0/lib64/libxl.so /usr/lib/libxl.so \
        && cp -r libxl-$XL_VERSION.0/include_c/ /usr/include/libxl_c \
        && mkdir php_excel \
        && wget -q https://github.com/Jan-E/php_excel/archive/$PHP_EXCEL.tar.gz \
        && tar -xf $PHP_EXCEL.tar.gz -C php_excel --strip-components=1 \
        && cd php_excel \
        && phpize \
        && ./configure \
          --with-libxml-dir=/usr/include/libxml2 \
          --with-libxl-incdir=/usr/include/libxl_c \
          --with-excel=shared \
        && make -j "$(nproc)" \
        && make install \
        && docker-php-ext-enable excel \
        && ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
        && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
        && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
        && docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
        && docker-php-ext-configure mysql --with-mysql=mysqlnd \
        && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
        && docker-php-ext-configure gmp \
        && docker-php-ext-install curl \
            ldap \
            soap \
            sysvmsg \
            sysvsem \
            sysvshm \
            mcrypt \
            gmp \
            bcmath \
            zip \
            gd \
            pcntl \
            sockets \
            wddx \
            gettext \
        && docker-php-ext-install pdo_mysql pdo_pgsql mysqli mysql pgsql \
        && pecl install ssh2 \
        && pecl install xdebug-$XDEBUG_VERSION \
        && pecl install -o -f redis \
        && docker-php-ext-enable ssh2 \
        && docker-php-ext-enable xdebug \
        && docker-php-ext-enable redis \
        && apt-get purge -y gcc cpp make zlib1g-dev linux-libc-dev libxml2-dev \
            libicu-dev libc6-dev libc-dev-bin $PKG_DEPS \
        && apt-get autoremove -y \
        && apt-get clean \
        && rm -rf /tmp/* /var/lib/apt/lists/*

RUN mkdir -p /var/www/html /var/www/html/static /var/www/html/media

RUN unset PHP_MD5 \
    && unset PHPIZE_DEPS \
    && unset PHP_CPPFLAGS \
    && unset XL_VERSION \
    && unset PKG_DEPS

COPY php.ini /usr/local/etc/php/conf.d/99-setting.ini

COPY excel.ini /usr/local/etc/php/conf.d/99-excel.ini

COPY phpinfo.php /var/www/html/phpinfo.php

WORKDIR /var/www/html

EXPOSE 80

CMD ["apache2-foreground"]
