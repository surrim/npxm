FROM php:fpm
WORKDIR /app

COPY bashrc /root/.bashrc
COPY --from=composer /usr/bin/composer /usr/local/bin/

RUN apt-get update

# bash-completion
RUN apt-get install -y bash-completion

# git
RUN apt-get install -y \
		git \
	&& git config --global --add safe.directory "*"

# gd
RUN apt-get install -y \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libpng-dev \
		libwebp-dev \
		libavif-dev \
	&& docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp --with-avif \
	&& docker-php-ext-install -j$(nproc) gd

# gmp
RUN apt-get install -y \
		libgmp-dev \
	&& docker-php-ext-install -j$(nproc) gmp

# imagick
RUN apt-get install -y \
		libmagickwand-dev \
		imagemagick \
	&& yes '' | pecl install imagick \
	&& docker-php-ext-enable imagick

# intl
RUN docker-php-ext-install -j$(nproc) intl

# opcache
RUN docker-php-ext-install -j$(nproc) opcache

# phpunit
RUN curl -#L https://phar.phpunit.de/phpunit.phar -o /usr/local/bin/phpunit
RUN chmod +x /usr/local/bin/phpunit

# pdo and mysqli
RUN docker-php-ext-install -j$(nproc) pdo pdo_mysql mysqli

# vim
RUN apt-get install -y \
		vim

# xdebug
RUN pecl install xdebug \
	&& docker-php-ext-enable xdebug

# zip
RUN apt-get install -y \
		libzip-dev \
		zip \
	&& docker-php-ext-install -j$(nproc) zip

RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
