FROM alterway/php:5.3-fpm

RUN docker-php-ext-install gettext

RUN pecl install -o -f redis \
        && rm -rf /tmp/pear \
        && docker-php-ext-enable redis

# 3.6.5 - from doc
# 3.8.4 - Last
ENV XL_VERSION 3.6.5

RUN wget http://libxl.com/download/libxl-lin-$XL_VERSION.tar.gz \
        && tar xfz libxl-lin-$XL_VERSION.tar.gz \
        && cp -p libxl-$XL_VERSION.0/lib64/libxl.so /usr/lib/libxl.so \
        && cp -r libxl-$XL_VERSION.0/include_c/ /usr/include/libxl_c \
        && rm -f libxl-lin-$XL_VERSION.tar.gz

RUN git clone https://github.com/Jan-E/php_excel.git php_excel \
        && cd php_excel \
        && phpize \
        && ./configure \
          --with-libxml-dir=/usr/include/libxml2 \
          --with-libxl-incdir=/usr/include/libxl_c \
          --with-excel=shared \
        && make -j "$(nproc)" \
        && make install \
        && docker-php-ext-enable excel

RUN apt-get purge -y gcc cpp make zlib1g-dev linux-libc-dev \
          libxml2-dev libicu-dev libc6-dev libc-dev-bin \
        && apt-get autoremove -y \
        && apt-get clean

RUN mkdir /application

RUN mkdir -p /application/static

RUN mkdir -p /application/media

RUN unset PHP_MD5 \
    && unset PHPIZE_DEPS \
    && unset PHP_CPPFLAGS \
    && unset XL_VERSION

COPY php.ini /usr/local/etc/php-fpm.d/50-setting.ini

COPY php-fpm.conf /usr/local/etc/php-fpm.conf

COPY phpinfo.php /application/phpinfo.php

WORKDIR /application

EXPOSE 9000

CMD ["php-fpm", "-F"]

