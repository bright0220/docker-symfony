# Docker Symfony (NGINX - MySQL - ELK)

[![Build Status](https://travis-ci.org/maxpou/docker-symfony.svg?branch=master)](https://travis-ci.org/maxpou/docker-symfony)

*Credit: This is a kind of fork from [eko/docker-symfony](https://github.com/eko/docker-symfony). Thanks to him :-)*

## Installation

1. Retrieve git project
```bash
$ git clone git@github.com:maxpou/docker-symfony.git
```
1. move your Symfony project into symfony folder
1. Build containers with
```bash
$ docker-compose up
```
1. Update your host file (add symfony.dev)
```bash
# get containers IP address and update host (replace IP according to your configuration)
$ docker inspect --format '{{ .NetworkSettings.IPAddress }}' $(docker ps -f name=php -q)
$ sudo echo "171.17.0.1 symfony.dev" >> /etc/hosts
```
1. Enjoy :-)

## Using

* Symfony app: visit [symfony.dev](http://symfony.dev)  
* Symfony dev mode: visit [symfony.dev/app_dev.php](http://symfony.dev/app_dev.php)  
* Logs (Kibana): [symfony.dev:81](http://symfony.dev:81)
* Logs (files location): logs/nginx and logs/symfony

## How it works?

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `application`: This is the Symfony application code container,
* `db`: This is the MySQL database container,
* `php`: This is the PHP-FPM container in which the application volume is mounted,
* `nginx`: This is the Nginx webserver container in which application volume is mounted too,
* `elk`: This is a ELK stack container which uses Logstash to collect logs, send them into Elasticsearch and visualize them with Kibana,
* `redis`: This is a redis database container.

This results in the following running containers:

```bash
$ docker-compose ps

           Name                          Command               State              Ports            
--------------------------------------------------------------------------------------------------
dockersymfony_application_1   /bin/bash                        Up                                  
dockersymfony_db_1            /entrypoint.sh mysqld            Up      0.0.0.0:3306->3306/tcp      
dockersymfony_elk_1           /usr/bin/supervisord -n -c ...   Up      0.0.0.0:81->80/tcp          
dockersymfony_nginx_1         nginx                            Up      443/tcp, 0.0.0.0:80->80/tcp
dockersymfony_php_1           php5-fpm -F                      Up      0.0.0.0:9000->9000/tcp      
dockersymfony_redis_1         /entrypoint.sh redis-server      Up      0.0.0.0:6379->6379/tcp      
```

## Useful commands

```bash
# Composer (e.g. composer update)
$ docker exec -ti $(docker ps -f name=php -q) sh -c  "cd /var/www/symfony/ && composer update"

# SF commands
$ docker exec -ti $(docker ps -f name=php -q) php /var/www/symfony/app/console cache:clear

# bash commands (no tab :( )
$ docker exec -ti $(docker ps -f name=php -q) /bin/sh

# MySQL commands
$ docker exec -ti $(docker ps -f name=db -q) mysql -uroot -p"root"

# Redis commands
$ docker exec -ti $(docker ps -f name=redis -q) sh -c 'exec redis-cli'

# F***ing cache folder
$ sudo chmod -R 777 symfony/app/cache
```

## TODO

[ ] MySQL -> PostgreSQL
[ ] Move SF app folder
