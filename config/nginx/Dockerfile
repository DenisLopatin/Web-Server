FROM nginx:latest
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y iputils-ping \
    git \
    nodejs \
    npm \
    php8.2 \
    php8.2-fpm \
    php8.2-xml \
    php8.2-curl \
    php8.2-mysql \
    php8.2-mysqli \
    nano \
    net-tools
CMD ["/bin/bash", "-c", "service php8.2-fpm start && chown www-data:www-data /usr/share/nginx/html/ -R && chmod g+w /usr/share/nginx/html/ -R && nginx -g 'daemon off;'"]
