FROM nginx:latest
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer
RUN apt update && apt upgrade
RUN apt install -y iputils-ping \
    git \
    nodejs  \
    npm \
    php8.2 \
    php8.2-fpm \
    php8.2-xml \
    php8.2-curl \
    nano \
    net-tools
CMD ["/bin/bash", "-c", "service php8.2-fpm start && chmod 755 /usr/share/nginx/html/* && nginx -g 'daemon off;'"]
