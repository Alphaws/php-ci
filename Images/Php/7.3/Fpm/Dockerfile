FROM php:7.3-fpm
MAINTAINER Imre Szeness <imre@prstart.co.uk>

ADD https://raw.githubusercontent.com/mlocati/docker-php-extension-installer/master/install-php-extensions /usr/local/bin/
RUN chmod uga+x /usr/local/bin/install-php-extensions && sync

ARG DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update -q \
    && apt-get install -qq -y \
       curl \
       git \
       zip \
       unzip \
       nodejs \
       npm

# Install build dependencies
RUN install-php-extensions \
    bcmath \
#    curl \
    exif \
    gd \
#    iconv \
    intl \
#    mbstring \
    pcntl \
#    pdo \
    pdo_mysql \
    pdo_pgsql \
#    pdo_sqlite \
    soap \
    sockets \
#    tokenizer \
#    xml \
    xsl \
    zip

# Composer
ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_MEMORY_LIMIT -1

# Composer
RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer \
  && composer global require "squizlabs/php_codesniffer=*"

# set user
RUN groupadd -g 1000 app \
      && useradd -d /home/app/ -u 1000 -s /bin/bash -g 1000 app \
      && mkdir -p /var/www/html /sock \
      && chown -R app:app /var/www /sock

# other
USER app:app
WORKDIR /var/www/html
VOLUME /var/www
EXPOSE 9000
