FROM php:7.4-fpm

RUN apt-get \
    update && \
    apt-get install -y --no-install-recommends \
    iputils-ping \
    net-tools \
    libbz2-dev \
    sqlite3 \
    bzip2 \
    libsqlite3-dev \
    libcurl4-openssl-dev \
    libreadline-dev \
    libtidy-dev \
    libxslt-dev \
    libzip-dev \
    libicu-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libssl-dev \
    libwebp-dev \
    libxpm-dev \
    libmcrypt-dev \
    libenchant-2-dev \
    libgmp-dev \
    libonig-dev

RUN docker-php-ext-install  \
    bcmath \
    bz2 \
    ctype \
    curl \
    dba \
    dom \
    exif \
    ffi \
    fileinfo \
    filter \
    ftp \
    gd \
    gettext \
    gmp \
    iconv \
    intl \
    mbstring \
    mysqli \
    opcache \
    pcntl \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pdo_sqlite \
    pgsql \
    phar \
    posix \
    session \
    shmop \
    simplexml \
    soap \
    sockets \
    sysvmsg \
    sysvsem \
    sysvshm \
    tidy \
    xml \
    xmlwriter \
    xsl \
    calendar \
    zip

RUN apt-get install -y \
        libmagickwand-dev \
		libc-client-dev \
		libkrb5-dev \
	&& docker-php-ext-configure imap \
		--with-kerberos \
		--with-imap-ssl \
	&& docker-php-ext-install -j$(nproc) \
		imap \
	&& apt-get purge -y \
		libc-client-dev \
		libkrb5-dev

COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/
RUN install-php-extensions imagick/imagick@master

RUN docker-php-ext-configure \
    gd \
      --prefix=/usr \
      --with-jpeg \
      --with-webp \
      --with-xpm \
      --with-freetype

RUN pecl install xdebug-3.1.6
RUN docker-php-ext-enable xdebug
