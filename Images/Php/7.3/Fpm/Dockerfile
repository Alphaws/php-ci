FROM php:7.3-fpm-alpine
MAINTAINER Imre Szeness <imre@prstart.co.uk>

ARG DOCKER_USER_UID=false
ARG DOCKER_USER_GID=false

ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    curl-dev \
    imagemagick-dev \
    libtool \
    libxml2-dev \
    postgresql-dev \
    sqlite-dev \
  && apk add --no-cache \
    curl \
    git \
    imagemagick \
    mysql-client \
    postgresql-libs \
    libintl \
    icu \
    icu-dev \
    libzip-dev \
  && pecl install imagick \
  && docker-php-ext-enable imagick \
  && docker-php-ext-install \
    bcmath \
    curl \
    iconv \
    mbstring \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pdo_sqlite \
    pcntl \
    tokenizer \
    xml \
    zip \
    intl \
  && curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer \
  && composer global require "squizlabs/php_codesniffer=*"
  && apk del -f .build-deps

WORKDIR /var/www/html
VOLUME /var/www
EXPOSE 9001
