ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}-alpine

LABEL maintainer="tao"

RUN \
    # 使用国内源
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && echo 'http://mirrors.ustc.edu.cn/alpine/v3.5/main' >> /etc/apk/repositories \
    && echo 'http://mirrors.ustc.edu.cn/alpine/v3.5/community' >> /etc/apk/repositories \
    && apk update \
    # 设置时区
    && apk add tzdata \
    && ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && echo "Asia/Shanghai" > /etc/timezone \
    && apk --update add wget \
          curl \
          git \
          build-base \
          libmemcached-dev \
          libmcrypt-dev \
          libxml2-dev \
          pcre-dev \
          zlib-dev \
          autoconf \
          cyrus-sasl-dev \
          libgsasl-dev

# 安装扩展
RUN docker-php-ext-install mysqli mbstring pdo pdo_mysql tokenizer xml pcntl bcmath sockets
RUN pecl channel-update pecl.php.net \
    && pecl install mcrypt-1.0.1 mongodb \
    && docker-php-ext-enable mongodb

# Add a non-root user:
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN addgroup -g ${PGID} swoolecli && \
    adduser -D -G swoolecli -u ${PUID} swoolecli

# Install Redis package:
RUN \
    # Install Redis Extension
    printf "\n" | pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

###########################################################################
# Swoole EXTENSION
###########################################################################

RUN \
    # Install Php Swoole Extension
    pecl install swoole-4.4.12 && docker-php-ext-enable swoole


# Install sdebug package:
WORKDIR /tmp
RUN \
  git clone https://github.com/mabu233/sdebug.git \
  && cd sdebug \
  && sh rebuild.sh \
  && docker-php-ext-enable xdebug

#
#--------------------------------------------------------------------------
# Optional Software's Installation
#--------------------------------------------------------------------------
#
# If you need to modify this image, feel free to do it right here.
#
    # -- Your awesome modifications go here -- #

#
#--------------------------------------------------------------------------
# Check PHP version
#--------------------------------------------------------------------------
#

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."

# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#

# Clean up
RUN rm /var/cache/apk/* \
    && rm -rf /tmp/* \
    && mkdir -p /var/www

WORKDIR /var/www

#CMD ["sh"]
#
#EXPOSE 9000
