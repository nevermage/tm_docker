FROM php:8.3-fpm-alpine

ENV USER bihunov

# Install tools required for build stage
RUN apk add --update --no-cache \
    bash curl wget rsync ca-certificates openssl openssh git tzdata openntpd \
    libxrender fontconfig libc6-compat \
    mysql-client gnupg binutils-gold autoconf \
    g++ gcc gnupg libgcc linux-headers make python3

# Install composer
COPY ./composer.* /var/www/html/
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer \
 && chmod 755 /usr/bin/composer

# Install additional PHP libraries
#RUN docker-php-ext-install bcmath pdo_mysql
RUN docker-php-ext-install bcmath mysqli pdo_mysql
RUN docker-php-ext-enable pdo_mysql mysqli
#RUN docker-php-ext-configure gd --with-freetype --with-jpeg
#RUN docker-php-ext-install gd

# Install libraries for compiling GD, then build it
RUN apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev \
 && docker-php-ext-install gd \
 && apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev

# Add ZIP archives support
RUN apk add --update --no-cache zlib-dev libzip-dev \
 && docker-php-ext-install zip

# Install xdebug
RUN pecl install xdebug \
 && docker-php-ext-enable xdebug

# Enable XDebug
#ADD xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN echo "xdebug.mode = debug" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.start_with_request = yes" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    echo "xdebug.discover_client_host = 1" >> /usr/local/etc/php/conf.d/xdebug.ini && \
    touch /usr/local/etc/php/conf.d/php.ini && \
    echo "setsebool -P httpd_can_network_connect 1" > /usr/local/etc/php/conf.d/php.ini


RUN addgroup --system --gid 1000 $USER && \
   adduser --system -u 1000 -g 1000 --home /code $USER
USER $USER

WORKDIR /var/www