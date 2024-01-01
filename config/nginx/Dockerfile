FROM nginx:latest
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y iputils-ping \
    git \
    nodejs \
    npm \
    php8.2 \
    php8.2-cli \
    php-zip \
    nano \
    net-tools
CMD ["/bin/bash", "-c", "chown www-data:www-data /usr/share/nginx/html/ -R && chmod 777 /usr/share/nginx/html/ -R && nginx -g 'daemon off;'"]
