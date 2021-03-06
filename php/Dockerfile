FROM php:7.1.11-fpm

MAINTAINER Tyee Noprom <tyeenoprom@gmail.com>

# Fix docker-php-ext-install script error
RUN sed -i 's/docker-php-\(ext-$ext.ini\)/\1/' /usr/local/bin/docker-php-ext-install

# Install env
ADD sources.list    /etc/apt/sources.list
RUN apt-get update && apt-get install -y --force-yes \
        git \
        vim \
        autoconf \
        pkg-config \
        libssl1.0.0=1.0.1t-1+deb8u6 \
        libgearman-dev \
        libmemcached-dev \
        libmcrypt-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng12-dev \
        libfreetype6 \
        libjpeg62-turbo \
        libmcrypt4 \
        libpng12-0 \
        libssl-dev \
        sendmail --no-install-recommends \
        && rm -r /var/lib/apt/lists/*

# Install other needed extensions
RUN buildDeps=" \
        libfreetype6-dev \
        libjpeg-dev \
        libldap2-dev \
        libmcrypt-dev \
        libpng12-dev \
        zlib1g-dev \
    "; \
    set -x \
    && apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
COPY redis-3.1.4.tgz /home/redis.tgz
COPY pdo_mysql.tgz /home/pdo_mysql.tgz
COPY mongodb-1.3.2.tgz /home/mongodb.tgz

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install zip \
        && docker-php-ext-install iconv \
        && docker-php-ext-install gd \
        && docker-php-ext-install mbstring \
        && docker-php-ext-install mcrypt \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-configure gd --enable-gd-native-ttf --with-jpeg-dir=/usr/lib/x86_64-linux-gnu --with-png-dir=/usr/lib/x86_64-linux-gnu --with-freetype-dir=/usr/lib/x86_64-linux-gnu \
        && docker-php-ext-install gd \
        && docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu \
        && docker-php-ext-install ldap \
        && docker-php-ext-install mbstring \
        && docker-php-ext-install mcrypt \
        && docker-php-ext-install mysqli \
        && docker-php-ext-install zip \
        && apt-get purge -y --auto-remove $buildDeps

RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini \
    && pecl install /home/mongodb.tgz && echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/mongodb.ini

# PHP config
RUN mkdir -p /app/log/php
ADD php.ini /usr/local/etc/php/php.ini
ADD php-fpm.conf /usr/local/etc/php-fpm.conf

# Composer
ADD composer.phar /usr/local/bin/composer
RUN chmod 755 /usr/local/bin/composer

WORKDIR /app

# Write Permission
RUN usermod -u 1000 www-data

EXPOSE 9000
VOLUME ["/app"]