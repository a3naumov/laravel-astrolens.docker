ARG PHP_VERSION=8.4.7-fpm-alpine3.21
ARG COMPOSER_VERSION=2.8.9

FROM composer:${COMPOSER_VERSION} as composer
FROM php:${PHP_VERSION}

# Install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Install system dependencies
RUN apk add --no-cache \
    curl \
    git \
    bash \
    linux-headers \
    zip \
    libpq-dev \
    $PHPIZE_DEPS \
;

# Install PHP extensions
RUN docker-php-ext-install \
    pdo_pgsql \
;

# Install xdebug
ARG INSTALL_XDEBUG=false
RUN if [ "${INSTALL_XDEBUG}" = "true" ]; then \
    pecl install xdebug && \
    docker-php-ext-enable xdebug; \
fi
ADD ./conf/ext/php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Create a user and group for the application
ARG USER_NAME=www
ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} ${USER_NAME} && \
    adduser -D -u ${USER_ID} -G ${USER_NAME} ${USER_NAME}
USER ${USER_NAME}

CMD ["php-fpm"]
