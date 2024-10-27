### Local web-server for development

You will need docker and docker-compose.

__Server include__:

`Nginx`, `MySQL (PhpMyAdmin)`, `MariaDB (PhpMyAdmin)`, `PostgreSQL (PGAdmin)`,
`Redis`, `PHP-fpm (php7.4, php8.3, xdebug)`.

The configuration files for all services are located in the `config` folder.

All the main configuration files for the services are also listed there.

### Ports

    80: Nginx
    443: Nginx
    3306: MySQL
    33060: MariaDB
    5432: Postgresql
    3000: PhpMyAdmin for MySQL
    4000: PhpMyAdmin for MariaDB
    5000: PGAdmin for Postgresql
    6379: Redis

### Getting Start

You need to add the site to the www directory.

Below is a step-by-step guide for launching websites in development mode.

__Configuring Hosts__

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

Create nginx configuration file for it in `config/nginx/conf.d` as
`[www_folder_name].conf`. Run `docker compose up -d` dor start.

For example, for a domain `your_domain.com` you need to create 
`your_domain.conf` file in the directory `config/nginx/conf.d`.

For PHP sites, you need to enable the following settings in the nginx config:

    location ~ \.php$ {
        include /etc/nginx/fastcgi_params;
        fastcgi_pass php{version|service}-fpm:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

The `fastcgi_pass` parameter should be called by the name of the `php-fpm` container.

__Important note__

Your files must belong to the www-data user. After starting the container, this should happen
by default, but if this is not the case, then run the following command:

    sudo chown -R www-data:www-data your_priject

This will assign a user and a group in the `nginx` docker container by setting it to `www-data`.

You will have to make the file writable for all users if the framework
you are working with requires it:

    sudo chmod 644 some.log

In some cases, you will need to change the file access rights that were
created by the www-data (Nginx) user. To do this execute the following
commands, for example, for laravel:

    chmod -R 777 vendor -R
    chmod -R 777 composer.lock

It is quite safe for local development. It is not necessary to send
these changes to a remote repository.

You may need to run the following command to undo changes in the version
control system:

    git config core.fileMode false

__Configuring SSL__

Log into the container: `docker exec -it 3ae77a23d951 bash`.
Here `3ae77a23d951` is the container ID printed by `docker ps` command.
You probably have another one, replace it.

In the container or locally, run the following commands:

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

For example, basic config for WordPress:

    server {
        listen 80;
        server_name wordpress;
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl;
        listen [::]:443 ssl;
    
        ssl_certificate     /etc/nginx/certs/server.crt;
        ssl_certificate_key /etc/nginx/certs/server.key;
        ssl_verify_client off;
    
        server_name wordpress;
        root /usr/share/nginx/html/wordpress;
        index index.php;
     
        location = /favicon.ico {
            log_not_found off;
            access_log off;
        }
    
        location = /robots.txt {
            allow all;
            log_not_found off;
            access_log off;
        }
    
        location ~ /\. {
            deny all;
        }
    
        location ~* /(?:uploads|files)/.*\.php$ {
            deny all;
        }
    
        location / {
            try_files $uri $uri/ /index.php?$args;
        }
    
        location ~ \.php$ {
            fastcgi_pass php8.3-fpm:9000;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
            fastcgi_hide_header X-Powered-By;
        }
    
        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
            expires max;
            log_not_found off;
        }
    }

Basic config for Laravel:

    server {
        listen 80;
        server_name laravel;
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl;
        listen [::]:443 ssl;
    
        ssl_certificate     /etc/nginx/certs/server.crt;
        ssl_certificate_key /etc/nginx/certs/server.key;
        ssl_verify_client off;
    
        server_name laravel;
        root /usr/share/nginx/html/laravel/public;
        index index.php;
     
        add_header X-Frame-Options "SAMEORIGIN";
        add_header X-Content-Type-Options "nosniff";
     
        charset utf-8;
     
        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }
     
        location = /favicon.ico { access_log off; log_not_found off; }
        location = /robots.txt  { access_log off; log_not_found off; }
     
        error_page 404 /index.php;
     
        location ~ \.php$ {
            fastcgi_pass php8.3-fpm:9000;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
            fastcgi_hide_header X-Powered-By;
        }
     
        location ~ /\.(?!well-known).* {
            deny all;
        }
    }

You can view the standard Nginx setup for a specific project.
For example, for [WordPress](https://www.nginx.com/resources/wiki/start/topics/recipes/wordpress/)
or for [Laravel](https://laravel.com/docs/10.x/deployment#nginx).

For more information, see [Nginx docs](https://nginx.org/en/docs/).

### Configuring php-fpm

It does not need a basic configuration, but all configs for configuration 
are located here - `config/php-fpm`.

__Xdebug__

TODO

### Configuring databases

You need to install clients to connect to databases, for example, such as 
`mysql-client` for `MySQL` or `redis-tools` for `Redis`. It is not necessary to 
install server parts:

    apt install postgresql-client
    apt install mysql-client

If you have an application installed on your device, for example, MySQL,
you should run the following command to connect to mysql in a docker
container, not on your device:

    mysql -uroot -h localhost -P 3306
    mysql -uroot -h 127.0.0.1 -P 3306

To connect to postgresql inside the container, use the following command:

    psql -h localhost -p 5432 -U postgres --password

__Dumps__

Take a dump of your database and put it in the directory `config/[db]/databases/`.
Each time the container is started, all sql files will be imported into 
the container. 

Therefore, if you need to save the changes, you need to dump the database
and put it in the directory `config/[db]/databases/`.

Don't forget to add instructions `CREATE DATABASE IF NOT EXISTS [db_name]` 
and `USE [db_name]` to the beginning of the sql file.

For MySql:

    ...
    CREATE DATABASE IF NOT EXISTS my_database;
    USE my_database;
    ...

You can always recreate the database by returning it to its original state 
in case of data loss. Just restart the container. Do not forget to make
fresh dumps at the end of the work.

### Pay attention

For applications that will connect to the database, you need to set 
the `host` as `mysql|mariadb|postgres` by `container_name`, 
not `localhost` or `127.0.0.1`.

When accessing a resource through a web browser, the connection to the 
database service will be performed by an internal service in docker, 
such as `php-fpm`. If you need to execute a command from the terminal, 
then the `mysql` service installed locally will no longer know about 
the `mariadb` server, for the local host it will be `127.0.0.1:33060`.

Therefore, when interacting with local commands such as `php artisan migrate` 
for `Laravel`, you need to change this to `127.0.0.1` in the `.env` file.

Therefore, in some cases, when launching the program through the terminal,
you need to go into the application configuration and change the data, 
for example:

    DB_CONNECTION=mysql
    DB_HOST=mariadb
    DB_PORT=3306

replace with:

    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=33060
