ARG PHP_VERSION

FROM laradock/php-fpm:2.2-${PHP_VERSION}

LABEL maintainer="Ricardo Pires <ricardo.tech@live.com>"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update -yqq && \
  apt-get install -y apt-utils && \
  pecl channel-update pecl.php.net

###########################################################################
# Opcache:
###########################################################################

RUN docker-php-ext-install opcache

# Copy opcache configration
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

###########################################################################
# Check PHP version:
###########################################################################

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

COPY ./laravel.ini /usr/local/etc/php/conf.d
COPY ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

USER root

# Clean up
RUN apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
  rm /var/log/lastlog /var/log/faillog

RUN usermod -u 1000 www-data

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
