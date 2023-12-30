FROM php:8.3-fpm
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y iputils-ping \
    nano \
    net-tools
RUN docker-php-ext-install mysqli
