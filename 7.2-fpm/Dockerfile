FROM php:7.2-fpm-alpine
MAINTAINER Imre Szeness <imre@prstart.co.uk>

ARG DOCKER_PHP_ENABLE_MYSQL
ARG DOCKER_PHP_ENABLE_REDIS
ARG DOCKER_PHP_ENABLE_XDEBUG
ARG DOCKER_USER_UID=false
ARG DOCKER_USER_GID=false

# Enable Redis
RUN if [ "${DOCKER_PHP_ENABLE_REDIS}" != "off" ]; then \
      pecl install redis && \
      docker-php-ext-enable redis && \
      php -m; \
    else \
      echo "Skip redis support"; \
    fi

# Add user for php-cli
RUN if [ "${DOCKER_USER_GID}" != "false" ] && [ "${DOCKER_USER_UID}" != "false" ]; then \
      addgroup -g ${DOCKER_USER_GID} php-cli && \
      adduser -D -u ${DOCKER_USER_UID} -G php-cli php-cli; \
    else \
      echo "No user for php-cli"; \
    fi

EXPOSE 9000
WORKDIR /var/www