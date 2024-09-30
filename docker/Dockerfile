FROM php:8.1-fpm-alpine

ENV APP_HOME /var/www/html
ENV USERNAME=www-data

RUN set -o errexit -o nounset -o pipefail -o xtrace; \
    \
    # Установить системные пакеты.
    apk update; \
    apk upgrade; \
    apk \
        add --no-cache \
            aspell-dev \
            autoconf \
            bash \
            build-base \
            bzip2 \
            bzip2-dev \
            curl \
            ffmpeg \
            freetype-dev \
            git \
            hiredis-dev \
            icu \
            imagemagick-c++ \
            imagemagick-dev \
            libaio-dev \
            libbz2 \
            libjpeg-turbo-dev \
            libmcrypt-dev \
            libpng \
            libpng-dev \
            librdkafka-dev \
            libstdc++ \
            libtool \
            libwebp \
            libxml2-dev \
            libxslt-dev \
            libzip \
            libzip-dev \
            linux-headers \
            make \
            nano \
            oniguruma \
            openldap-dev \
            openssh \
            postgresql-dev \
            unzip \
            wget \
            yaml-dev \
            zip \
            zlib-dev; \
    \
    # Clean up.
    rm -fr /tmp/* /var/tmp/*

# Извлеките скрипт, используемый для установки расширений PHP из исходного контейнера.
COPY --from=mlocati/php-extension-installer:2.1.10 /usr/bin/install-php-extensions /usr/local/bin/

RUN set -o errexit -o nounset -o pipefail -o xtrace; \
    \
    # Установить расширения PHP
    install-php-extensions \
        apcu \
        bcmath \
        gd \
        igbinary \
        intl \
        ldap \
        pcntl \
        pdo_pgsql \
        redis \
        sockets \
        sqlsrv \
        xdebug \
        zip

# Установка Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN set -o errexit -o nounset -o pipefail -o xtrace; \
    \
    composer --global config repos.packagist composer 'https://mirrors.tencent.com/composer/'

RUN chmod +x /usr/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER=1

# рабочий каталог
WORKDIR $APP_HOME

# Установка пользователя
USER ${USERNAME}

# Копирование файлов проекта
COPY --chown=${USERNAME}:${USERNAME} ../ $APP_HOME/

RUN composer install

EXPOSE 9000