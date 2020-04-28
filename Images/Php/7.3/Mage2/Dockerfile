FROM php:7.3-alpine
MAINTAINER Imre Szeness <imre@prstart.co.uk>

# Install dev dependencies
RUN apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS \
    curl-dev \
    icu-dev \
    imagemagick-dev \
    libtool \
    libxml2-dev \
    libxslt-dev \
    postgresql-dev \
    sqlite-dev


# Install production dependencies
RUN apk add --no-cache \
    bash \
    curl \
    freetype-dev \
    g++ \
    gcc \
    git \
    icu-libs \
    imagemagick \
    libc-dev \
    libintl \
    libjpeg-turbo-dev \
    libpng-dev \
    libxslt \
    libzip-dev \
    make \
    mysql-client \
    nodejs \
    nodejs-npm \
    openssh-client \
    postgresql-libs \
    rsync \
    yarn \
    zlib-dev

# Install PECL and PEAR extensions
RUN pecl install \
    imagick \
    xdebug

# Enable PECL and PEAR extensions
RUN docker-php-ext-enable \
    imagick \
    xdebug

# Configure php extensions
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure zip --with-libzip

# Install php extensions
RUN docker-php-ext-install \
    bcmath \
    calendar \
    curl \
    exif \
    gd \
    iconv \
    intl \
    mbstring \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pdo_sqlite \
    pcntl \
    sockets \
    soap \
    tokenizer \
    xml \
    xsl \
    zip

# Install composer
ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1
RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer

# Install PHP_CodeSniffer
RUN composer global require "squizlabs/php_codesniffer=*"

# Cleanup dev dependencies
RUN apk del -f .build-deps && rm -rf /var/cache/apk/*

# Setup working directory
WORKDIR /var/www
