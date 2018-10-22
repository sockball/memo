# @see https://hub.docker.com/_/php/
FROM php:7.2-fpm

# set timezone
#ENV TZ=Asia/Shanghai
#RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# extensions @see http://php.net/docs.php
# gd
# intl Internationalization extension
# bz2 transparently read and write bzip2 (.bz2) compressed files
# zip
# pcntl Process Control
# pdo_mysql
# sockets
# opache
# imagick
RUN apt-get update && apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && :\
    && apt-get install -y libicu-dev \
    && docker-php-ext-install intl \
    && :\
    && apt-get install -y libbz2-dev \
    && docker-php-ext-install bz2 \
    && :\
    && docker-php-ext-install zip \
    && docker-php-ext-install pcntl \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install sockets \
    && :\
    && apt-get install -y libmagickwand-dev \
    && pecl install imagick-3.4.3 \
    && docker-php-ext-enable imagick \
    && :\
    && pecl install xdebug-2.6.1 \
    && pecl install redis-4.1.1 \
    && docker-php-ext-enable xdebug redis \
    && rm -rf /tmp/pear
#    && docker-php-ext-install opcache \
#    && apt-get install -y libxml2-dev \
#    && apt-get install -y libxslt-dev \
#    && docker-php-ext-install soap \
#    && docker-php-ext-install xsl \
#    && docker-php-ext-install xmlrpc \
#    && docker-php-ext-install wddx \
#    && :\
#    && docker-php-ext-install pdo_oci \
#    && docker-php-ext-install oci8 \