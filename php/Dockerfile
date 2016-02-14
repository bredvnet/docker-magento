FROM php:5.6-fpm
MAINTAINER Björn Rennfanz <bjoern.rennfanz@br-edv.net>

RUN apt-get -qqy update \
 && apt-get -qqy install git \
                         libcurl4-gnutls-dev \
                         libmcrypt-dev \
                         libpng12-dev \
                         libxml2-dev \
                         libxslt-dev \
 && docker-php-ext-install curl \
                           bcmath \
                           gd \
                           mbstring \
                           mcrypt \
                           mysql \
                           opcache \
                           pdo_mysql \
                           soap \
                           xsl \
                           zip \
  # Idiopathic rm failure; loop is workaround
 && until rm -rf /var/lib/apt/lists; do sleep 1; done
RUN usermod -u 1000 www-data