### Local web-server for development

You will need docker and docker-compose.

__Server include__:

* Nginx
  * php-fpm
  * php-cli
  * git
  * nodejs
  * npm
* Database
  * MySQL
* PhpMyAdmin

### Getting Start

You need to add the site to the www directory. After that, create 
nginx configuration file for it in `config/nginx/conf.d` as 
`[www_folder_name].conf`. Run `docker compose up -d` dor start.

### Ports

- 80: Nginx
- 443 Nginx
- 3306: MariaDB
- 3000: PHPMyAdmin

### Networks

- app
  - nginx
  - mariadb
- database
  - mariadb
  - phpmyadmin

### Pay attention

For applications that will connect to the database, you need to set 
the `host` as `mariadb`. not `localhost` or `127.0.0.1`. 

The configuration files for `php-cli` are located in the `config/nginx/php-cli`
directory, for `php-fpm` in the directory `config/nginx/php-fpm`.
