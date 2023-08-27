### Local Web-Server for development

You will need docker and docker-compose.

__Server include__:

* Nginx
* Php-fpm
* MySQL
* PhpMyAdmin

### Getting Start

You need to add the site to the www directory. After that, create 
nginx configuration file for it in `config/nginx/conf.d`.
Run `docker compose up -d` dor start.
