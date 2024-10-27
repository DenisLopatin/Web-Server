FROM nginx:latest
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

RUN apt-get  \
    update && \
    apt-get install -y \
    nano \
    net-tools \
    iputils-ping \
    git \
    nodejs \
    npm \
    php  \
    php-cli  \
    php-common \
    php-mysql \
    php-pgsql \
    php-zip \
    php-gd \
    php-mbstring \
    php-curl \
    php-xml \
    php-bcmath \
    openssl  \
    php-json  \
    php-tokenizer \
    php-mysqli \
    php-bz2 \
    php-calendar \
    php-ctype \
    php-curl \
    php-dba \
    php-dom \
    php-enchant \
    php-exif \
    php-fileinfo \
    php-ftp \
    php-gd \
    php-gmp \
    php-iconv \
    php-imap \
    php-interbase \
    php-intl \
    php-ldap \
    php-mbstring \
    php-mcrypt \
    php-odbc \
    php-opcache \
    php-pdo \
    php-pgsql \
    php-phar \
    php-posix \
    php-pspell \
    php-readline \
    php-shmop \
    php-simplexml \
    php-snmp \
    php-soap \
    php-sockets \
    php-sysvmsg \
    php-sysvsem \
    php-sysvshm \
    php-tidy \
    php-xmlreader \
    php-xmlrpc \
    php-xmlwriter \
    php-xsl \
    php-zip

CMD ["/bin/bash", "-c", "find /usr/share/nginx/html/ \( -type f -execdir chmod -R 644 {} \; \) -o \( -type d -execdir chmod -R 711 {} \; \)"]
CMD ["/bin/bash", "-c", "chown -R www-data:www-data /usr/share/nginx/html/"]
CMD ["/bin/bash", "-c", "nginx -g 'daemon off;'"]
