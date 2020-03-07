FROM php:7.1-fpm

ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN set -x \
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24 \
    && apt-get update -y \
    && apt-get upgrade -y \
    && apt-get install -y zip unzip git locales --allow-unauthenticated \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/ \
    && echo "pt_BR.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    && docker-php-ext-install -j$(nproc) bcmath  \
    && pecl install xdebug-2.6.1 \
    && docker-php-ext-enable xdebug \
    && rm -rf /var/lib/apt/lists/* \
    && curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

RUN sed -ri 's/^www-data:x:82:82:/www-data:x:1000:1001:/' /etc/passwd

RUN php -m
