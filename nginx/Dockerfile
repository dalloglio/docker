FROM nginx:alpine

LABEL maintainer="Ricardo Pires <ricardo.tech@live.com>"

COPY nginx.conf /etc/nginx/

RUN apk update \
  && apk upgrade \
  && apk add --no-cache openssl \
  && apk add --no-cache bash \
  && adduser -D -H -u 1000 -s /bin/bash www-data

ARG PHP_UPSTREAM_CONTAINER=php-fpm
ARG PHP_UPSTREAM_PORT=9000

RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
  && rm /etc/nginx/conf.d/default.conf

ADD ./startup.sh /opt/startup.sh
RUN sed -i 's/\r//g' /opt/startup.sh
CMD ["/bin/bash", "/opt/startup.sh"]

EXPOSE 80 443
