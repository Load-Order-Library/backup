# Production build
FROM composer:2.5.7 as build-prod-composer

WORKDIR /app

RUN mkdir -p bootstrap/cache

COPY . .
RUN composer install --prefer-dist --no-dev --optimize-autoloader --no-interaction

##########################################################
# Production app
FROM php:8.2-alpine3.18 as backup-prod

ARG user=backupprod
ARG uid=7000
RUN adduser -u $uid -D $user

WORKDIR /srv/lol-backup

# Install system deps
RUN apk update && apk add \
    libzip-dev \
    mariadb-client

# Clear cache
RUN apk cache clean && \
    rm -rf /var/lib/apk/lists/* && \
    rm -rf /var/cache/apk/*

# Install needed PHP extensions
RUN docker-php-ext-install pdo_mysql zip

# Copy project to container
COPY . .
COPY --from=build-prod-composer /app/vendor ./vendor
COPY --from=build-prod-composer /app/bootstrap ./bootstrap

RUN chown -R $user:$user /srv/lol-backup

USER $user

