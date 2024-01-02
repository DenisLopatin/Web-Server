### Local web-server for development

You will need docker and docker-compose.

__Server include__:

* Nginx
  * php-cli
  * git
  * nodejs
  * npm
* Database
  * MariaDB
    * PhpMyAdmin
  * PostgreSQL
    * PGAdmin
* PHP-fpm
  * PHP7.4
  * PHP8.3

### Getting Start

You need to add the site to the www directory. After that, create 
nginx configuration file for it in `config/nginx/conf.d` as 
`[www_folder_name].conf`. Run `docker compose up -d` dor start.

### Ports

    80: Nginx
    443: Nginx
    3000: PhpMyAdmin
    4000: PGAdmin

### Networks

- app
  - nginx
  - mariadb
  - pgadmin4
  - php7.4-fpm
  - php8.3-fpm
- database
  - nginx
  - mariadb
  - postgresql
  - pgadmin4
  - phpmyadmin

### Configuring Hosts

Open the hosts file and add the aliases for 127.0.0.1:

    127.0.0.1 your_domain.com
    127.0.0.1 subdomain.your_domain.com

In most cases, it will be accessible via the following path:

`C:\Windows\System32\drivers\etc` for Windows

`/etc/hosts` for Linux

`/private/etc/hosts` for macOS

### Configuring Nginx

The Nginx config is located in `config/nginx/conf.d`. You can continue to 
work with the default config or create a new one based on the domain name
that you assigned alias to in the host file. 

For example, for a domain `your_domain.com` you need to create 
`your_domain.conf` file in the directory `config/nginx/conf.d`.

For PHP sites, you need to enable the following settings in the nginx config:

    location ~ \.php$ {
        include /etc/nginx/fastcgi_params;
        fastcgi_pass php{version|service}-fpm:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

### Database import

Take a dump of your database and put it in the directory `config/mariadb/databases/`.
Each time the container is started, all sql files will be imported into 
the container. 

### Configuring SSL

Log into the container: `docker exec -it 3ae77a23d951 bash`. 
Here `3ae77a23d951` is the container ID printed by docker ps. 
You probably have another one, replace it.

In the container, run the commands:

    cd /etc/nginx
    mkdir certs
    cd certs
    openssl req -newkey rsa:2048 -nodes -keyout server.key -x509 -days 365 -out server.crt

The last command will ask you five questions about certificate identification,
generate a 2048-bit RSA key in the `/etc/nginx/certs/server.key` file and 
the certificate in the `/etc/nginx/certs/server.crt` file. The validity 
period of the certificate is 365 days, the key is without a password.

This will have to be done once, and on subsequent launches you will copy 
them from your file system to the container.

Edit your server settings. In the example below, the default server 
settings are edited:

    server {
        listen       80;
        listen  [::]:80;
        listen  443 ssl; # +
    
        ssl_certificate     /etc/nginx/certs/server.crt; # +
        ssl_certificate_key /etc/nginx/certs/server.key; # +
        ssl_verify_client off; # +
        ... # the rest is at your discretion according to the nginx settings

### Pay attention

For applications that will connect to the database, you need to set 
the `host` as `mariadb` or `postgres` by `container_name`, 
not `localhost` or `127.0.0.1`.

The configuration files for all services are located in the `config` folder.

Sometimes the Nginx server may be unavailable after the container is built.
You need to go into the container and execute the following commands:

    service nginx status
    service nginx -t
    service nginx restart

In some cases, you will need to change the file access rights that were 
created by the www-data (Nginx) user. To do this execute the following 
commands, for example, for laravel:

    chmod -R 777 vendor -R
    chmod -R 777 composer.lock

If you have an application installed on your device, for example, MySQL,
you should run the following command to connect to mysql in a docker 
container, not on your device:

    mysql -uroot -h localhost -P 3306
    mysql -uroot -h 127.0.0.1 -P 3306

To connect to postgresql inside the container, use the following command:

    psql -h localhost -p 5432 -U postgres --password

### TODO

`config/postgresql/postgresql.conf` not included in the container.
