FROM php:8.2-apache

# Add ServerName to prevent Apache warnings
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

ENV COMPOSER_ALLOW_SUPERUSER=1 \
    NODE_VERSION=18.17.0 \
    NVM_DIR=/root/.nvm \
    PATH="/root/.nvm/versions/node/v18.17.0/bin/:$PATH"

# Install system dependencies
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        curl \
        git \
        nano \
        zip \
        unzip \
        libzip-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libxml2-utils \
        mariadb-client \
        ca-certificates \
        gnupg \
        lsb-release \
        build-essential \
        autoconf \
        pkg-config && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) \
        gd \
        zip \
        mysqli \
        pdo \
        pdo_mysql && \
    pecl install xdebug && \
    docker-php-ext-enable xdebug && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# PHP and Apache configuration
COPY ./docker/docker-php-ext-xdebug3-ext.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug-ext.ini
COPY ./docker/docker-php-extra.ini /usr/local/etc/php/conf.d/docker-php-extra.ini

# Utility scripts and bash setup
COPY ./docker/bash /root/bash
COPY ./docker/firstRun.sh /root/firstRun.sh
# RUN bash /root/firstRun.sh

# Install Node.js via NVM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    . "$NVM_DIR/nvm.sh" && \
    nvm install $NODE_VERSION && \
    nvm use $NODE_VERSION && \
    nvm alias default $NODE_VERSION && \
    npm install -g npm@10.2.1 && \
    node -v && npm -v
