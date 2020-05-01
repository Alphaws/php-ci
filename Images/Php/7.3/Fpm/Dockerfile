FROM php:7.3-fpm-alpine
MAINTAINER Imre Szeness <imre@prstart.co.uk>
# Build options
ARG DOCKER_USER_UID=false
ARG DOCKER_USER_GID=false
ARG DOCKER_PHP_ENABLE_IMAGICK
ARG DOCKER_PHP_ENABLE_XDEBUG
ARG DOCKER_PHP_ENABLE_REDIS
# Info
RUN echo -n "Php Docker image:" && \
    echo -n "With xdebug support:        " ; if [[ "${DOCKER_PHP_ENABLE_XDEBUG}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi && \
    echo -n "With imagick support:       " ; if [[ "${DOCKER_PHP_ENABLE_IMAGICK}" = "on" ]] ;      then echo "Yes"; else echo "No" ; fi && \
    echo -n "With redis support:         " ; if [[ "${DOCKER_PHP_ENABLE_REDIS}" = "on" ]] ;    then echo "Yes"; else echo "No" ; fi && \
    if [ -z ${DOCKER_USER_UID+x} ]; then echo "DOCKER_USER_UID is unset"; DOCKER_USER_UID=1000; else echo "DOCKER_USER_UID is set to '$DOCKER_USER_UID'"; fi && \
    if [ -z ${DOCKER_USER_GID+x} ]; then echo "DOCKER_USER_GID is unset"; DOCKER_USER_GID=1000; else echo "DOCKER_USER_GID is set to '$DOCKER_USER_GID'"; fi
# Composer
ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1
# Install dependencies
RUN apk add --update --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    icu-libs \
    libintl \
    curl-dev \
    libzip \
    imagemagick-dev \
    libtool \
    libxml2-dev \
    postgresql-dev \
    sqlite-dev \
    git \
    patch
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
    git \
    patch
# configure php extensions
RUN docker-php-ext-configure soap && \
    docker-php-ext-configure opcache --enable-opcache && \
    docker-php-ext-configure zip --enable-zip --with-libzip && \
    docker-php-ext-configure intl --enable-intl
# install php extensions
RUN docker-php-ext-install \
    bcmath \
    curl \
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
    tokenizer \
    xml \
    zip
# Enable Redis
RUN if [ "${DOCKER_PHP_ENABLE_REDIS}" != "off" ]; then \
      pecl install redis && \
      docker-php-ext-enable redis && \
      php -m; \
    else \
      echo "Skip redis support"; \
    fi
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
RUN if [ "${DOCKER_USER_GID}" != "false" ] && [ "${DOCKER_USER_UID}" != "false" ]; then \
      addgroup -g ${DOCKER_USER_GID} php-cli && \
      adduser -D -u ${DOCKER_USER_UID} -G php-cli php-cli; \
    else \
      echo "No user for php-cli"; \
    fi
# other
WORKDIR /var/www/html
VOLUME /var/www
EXPOSE 9000
