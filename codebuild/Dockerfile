FROM php:7.3.11-fpm

ENV TZ=America/Sao_Paulo
ENV AWS_ACCESS_KEY_ID=
ENV AWS_SECRET_ACCESS_KEY=

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN set -x \
RUN apt-key a   dv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys E1DD270288B4E6030699E45FA1715D88E1DF1F24 \
    && apt-get update -y \
    && apt-get upgrade -y \
    && apt-get install -y zip unzip git locales python3-pip apt-transport-https ca-certificates curl gnupg-agent software-properties-common --allow-unauthenticated \
    && curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian buster stable" \
    && apt-get update -y \
    && apt-get install docker-ce docker-ce-cli containerd.io -y \
    && pip3 install --upgrade awscli \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/ \
    && echo "pt_BR.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen \
    && docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-install -j$(nproc) bcmath  \
    && rm -rf /var/lib/apt/lists/* \
    && curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

RUN sed -ri 's/^www-data:x:82:82:/www-data:x:1000:1001:/' /etc/passwd

RUN php -m
