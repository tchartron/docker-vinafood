ARG PHP_VERSION

FROM php:${PHP_VERSION}

WORKDIR /var/www

# Install packages
RUN apk update
RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        coreutils \
        freetype-dev \
        icu-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libtool \
        libwebp-dev \
        libzip-dev \
        libsodium-dev \
        zlib-dev \
        libxml2-dev \
        libssh-dev \
        oniguruma-dev \
        supervisor \
        curl \
        acl \
        openssl \
        # nodejs \
        # npm \
        # nginx \
        supervisor

#APCU (Not necessary ? how to enable ?)
# RUN pecl install apcu; \
#     pecl clear-cache

#Redis extension
RUN pecl install -o -f redis \
&&  rm -rf /tmp/pear \
&&  docker-php-ext-enable redis

# PHP extensions install
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-configure zip

RUN docker-php-ext-install -j$(nproc) \
        exif \
        gd \
        intl \
        zip \
        pdo \
        pdo_mysql \
        bcmath \
        ctype \
        # fileinfo \
        mbstring \
        # tokenizer \
        # opcache \
        xml
        # json \
        # simplexml \
        # sockets

ENV PHP_OPCACHE_ENABLE="0" \
    PHP_OPCACHE_VALIDATE_TIMESTAMPS="0" \
    PHP_OPCACHE_MAX_ACCELERATED_FILES="10000" \
    PHP_OPCACHE_MEMORY_CONSUMPTION="192" \
    PHP_OPCACHE_MAX_WASTED_PERCENTAGE="10"

# PHP configurations
# RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# Configure supervisord
RUN mkdir -p /var/log/supervisor
COPY .docker/dev/supervisord/supervisord.conf /etc/supervisor/conf.d/boootic.conf

# # Configure entrypoint
# COPY .docker/php/fresh_db.sh /usr/local/bin/fresh_db
# RUN chmod +x /usr/local/bin/fresh_db

# Composer latest
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer
