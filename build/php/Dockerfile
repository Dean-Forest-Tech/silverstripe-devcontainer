FROM php:8.2-fpm

ARG USER_ID
ARG GROUP_ID
ARG NODE_VERSION

# set env
ENV BASH_ENV /home/www-data/.bash_env

# Remove and re-create www-data users and map to local system ID
RUN if [ ${USER_ID:-0} -ne 0 ] && [ ${GROUP_ID:-0} -ne 0 ]; then \
    userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi &&\
    groupadd -g ${GROUP_ID} www-data &&\
    useradd -l -u ${USER_ID} -g www-data www-data &&\
    install -d -m 0755 -o www-data -g www-data /home/www-data &&\
    chown --changes --silent --no-dereference --recursive \
          --from=33:33 ${USER_ID}:${GROUP_ID} \
        /home/www-data \
;fi

COPY php.ini /usr/local/etc/php/php.ini

RUN apt-get update && apt-get install -y \
		curl \
		g++ \
		git-core \
		gzip \
		less \
        unzip \
		nano \
		openssh-client \
		xfonts-75dpi \
		xfonts-base \
		wget \
		zip \
		mariadb-client \
        net-tools \
		inetutils-ping \
		zlib1g-dev \
		libcurl4-openssl-dev \
		libgd-dev \
        libzip-dev \
        zlib1g-dev \
        libjpeg62-turbo-dev \
        libicu-dev \
        libpng-dev \
		libwebp-dev \
		libjpeg62-turbo-dev \
		libpng-dev libxpm-dev \
		libfreetype6-dev \
		libmagickwand-dev \
		libmcrypt-dev \
		libtidy-dev \
		libxslt-dev \
		libzip-dev \
		python3-pip \
		python3-full \
	--no-install-recommends && \
	apt-get autoremove -y && \
curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer && \
curl -sS http://silverstripe.github.io/sspak/install | php -- /usr/local/bin && \
apt-get autoremove -y && \
rm -r /var/lib/apt/lists/*

RUN docker-php-ext-configure zip && \
    docker-php-ext-configure gd --with-jpeg --with-freetype && \
    docker-php-ext-configure intl && \
    docker-php-ext-configure mysqli --with-mysqli=mysqlnd

RUN docker-php-ext-install -j$(nproc) \
		bcmath \
		intl \
		gd \
		mysqli \
		pdo \
		pdo_mysql \
		soap \
		tidy \
		xsl \
		zip

# Update PECL (build flagged this as a potential issue)
RUN pecl channel-update pecl.php.net

## Install imagick
RUN pecl install imagick && docker-php-ext-enable imagick

# Install and configure xdebug
RUN pecl install  xdebug-3.2.1 && docker-php-ext-enable xdebug
COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

USER www-data

# Use bash for the shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install Node via nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

COPY bashrc /home/www-data/.bashrc

# Install node
RUN source /home/www-data/.bashrc && nvm install ${NODE_VERSION}

# Install yarn and corepack
RUN source /home/www-data/.bashrc && npm install -g corepack
RUN source /home/www-data/.bashrc && corepack enable
RUN source /home/www-data/.bashrc && corepack install -g yarn@stable