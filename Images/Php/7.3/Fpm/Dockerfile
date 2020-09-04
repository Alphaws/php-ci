FROM php:7.3-fpm-alpine
MAINTAINER Imre Szeness <imre@prstart.co.uk>
# Build options
ARG DOCKER_PHP_ENABLE_IMAGICK
ARG DOCKER_PHP_ENABLE_XDEBUG
# Info
RUN echo -n "Php Docker image: " && \
    echo -n "With xdebug support:  " ; if [[ "${DOCKER_PHP_ENABLE_XDEBUG}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi && \
    echo -n "With imagick support: " ; if [[ "${DOCKER_PHP_ENABLE_IMAGICK}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi && \
# Composer
ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1
# Install dependencies
RUN apk update
RUN apk add --update --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    curl-dev \
    freetype-dev \
    git \
    icu-libs \
    imagemagick-dev \
    libintl \
    libjpeg-turbo-dev \
    libpng-dev \
    libtool \
    libxml2-dev \
    libxslt-dev \
    libzip \
    patch \
    postgresql-dev \
    sqlite-dev
# Install build dependencies
RUN apk add --update --no-cache --virtual .docker-php-global-dependancies \
    freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    curl \
    gettext-dev \
    git \
    imagemagick \
    mysql-client \
    postgresql-libs \
    libintl \
    icu \
    icu-dev \
    libzip-dev \
    libxslt \
    git \
    patch
# configure php extensions
RUN docker-php-ext-configure soap && \
    docker-php-ext-configure gd \
      --with-freetype-dir=/usr/include/ \
      --with-jpeg-dir=/usr/include/ \
      --with-png-dir=/usr/include/ && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure zip --enable-zip --with-libzip && \
    docker-php-ext-configure intl --enable-intl
# install php extensions
RUN docker-php-ext-install \
    bcmath \
    curl \
    exif \
    gd \
    iconv \
    intl \
    mbstring \
    pcntl \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pdo_sqlite \
    soap \
    sockets \
    tokenizer \
    xml \
    xsl \
    zip
# Enable Redis
RUN pecl install -o -f redis && \
    docker-php-ext-enable redis && \
    php -m
# Enable imagick
RUN if [ "${DOCKER_PHP_ENABLE_IMAGICK}" != "off" ]; then \
      # Dependancy for imagick \
      apk add --update --no-cache \
          imagemagick \
          imagemagick-libs && \
      # Build dependancy for imagick \
      apk add --update --no-cache --virtual .docker-php-imagick-dependancies \
          imagemagick-dev && \
      pecl install imagick && \
      docker-php-ext-enable imagick && \
      apk del .docker-php-imagick-dependancies && \
      php -m; \
    else \
      echo "Skip imagick support"; \
    fi
# Enable Xdebug
RUN if [ "${DOCKER_PHP_ENABLE_XDEBUG}" != "off" ]; then \
      # Build dependancy for XDebug \
      apk add --update --no-cache --virtual .docker-php-xdebug-dependancies \
          bash \
          git && \
      git clone https://github.com/xdebug/xdebug.git && \
      cd xdebug && \
      ./rebuild.sh && \
      docker-php-ext-enable xdebug && \
      echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      rm -rf xdebug && \
      apk del .docker-php-xdebug-dependancies && \
      php -m; \
    else \
      echo "Skip xdebug support"; \
    fi
# Composer
RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer \
  && composer global require "squizlabs/php_codesniffer=*"
# Clean
RUN apk del .docker-php-global-dependancies \
  && rm -rf /var/cache/apk/* \
  && docker-php-source delete
# set user
RUN addgroup -g 1000 app \
      && adduser -D -u 1000 -G app app \
      && mkdir -p /var/www/html /sock \
      && chown -R app:app /var/www /sock
# other
USER app:app
WORKDIR /var/www/html
VOLUME /var/www
EXPOSE 9000
