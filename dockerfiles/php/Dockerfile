FROM php:8.3-fpm-alpine3.19

# Instalando dependências de sistema.
RUN apk add --no-cache \
  bash \
  curl \
  git \
  libpng-dev \
  libzip-dev \
  zlib-dev \
  mariadb-dev \
  supervisor \
  tzdata

# Instalando extensões PHP
RUN docker-php-ext-install gd \
  && docker-php-ext-install zip \
  && docker-php-ext-install pdo_mysql

# Instalando o Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Instalando o node
RUN apk add --no-cache nodejs npm

# CMD ["supervisord", "-c", "/etc/supervisord.conf"]

ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Configurandoo diretório de trabalho
WORKDIR /var/www/app

EXPOSE 9000