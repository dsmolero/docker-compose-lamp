LAMP stack built with Docker Compose
====================================

This is a basic LAMP stack environment built using Docker Compose. It consists following:

* PHP 7.1
* Apache 2.4
* MySQL 5.7
* phpMyAdmin


## Requirements

Edit /etc/hosts file and add the following

    <your.docker.machine.ip>        dockerhost


## Installation

Clone this repository on your local computer and switch to branch `7.1.x`. 

Run the `docker-compose up -d`.

```shell
git clone https://github.com/dsmolero/docker-compose-lamp.git
cd docker-compose-lamp/
git fetch --all
git checkout 7.1.x
cp sample.env .env
docker-compose up -d
```

Wait a few moments for the mysql database server to stabilize.

Your LAMP stack is now ready!! You can access it via `http://dockerhost`.


## Integrate your Laravel Application

Shut down the docker containers:

    $ docker-compose down

Cleanup the volume leftovers manually (Careful with `docker-compose down -v`).

Place your laravel app inside the current directory. ex:

    $ cp -R ~/public_html/myapp myapp

> NO need to set group ownership of 
> `./myapp/storage` and `./myapp/bootstrap/cache` to `_www` or `www-data`


## Configuration

This package comes with default configuration options. You can modify them by creating `.env` file in your root directory.

To make it easy, just copy the content from `sample.env` file and update the environment variable values as per your need.

    $ cp sample.env .env
    $ nano .env

Put the following entries:

    PROJECT_ROOT = ./myapp

Edit your Laravel app configuration:

    $ nano myapp/.env

Put the following entries:

    DB_CONNECTION=mysql
    DB_HOST=mysql
    DB_PORT=3306
    DB_DATABASE=myapp
    DB_USERNAME=root
    DB_PASSWORD=tiger


### Configuration Variables

There are following configuration variables available and you can customize them by overwritting in your own `.env` file.

_**PROJECT_ROOT**_

The project directory. The document root for Apache server will be `${PROJECT_ROOT}/public`. The default value for 
this is `./www`.

_**VHOSTS_DIR**_

This is for virtual hosts. The default value for this is `./config/vhosts`. You can place your virtual hosts conf files here.

> Make sure you add an entry to your system's `hosts` file for each virtual host.

_**PHP_INI**_

Path to `php.ini` file. The default is at `./config/php/php.ini`.

_**APACHE_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/apache2`.

_**MYSQL_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/mysql`.

_**MYSQL_ROOT_PASSWORD**_

This will be the password for user root of the MySQL database server. The default value is `'tiger'`.


## Create the Application Database
    
Launch the docker containers:
    
    $ docker-compose up -d

Wait a few moments for the mysql database server to stabilize.

Open phpmyadmin in your browser:
  
    http://dockerhost:8080

Create the database for your app:
  
    myapp

Do the database migrations. In the terminal:

    $ docker-compose exec webserver php artisan migrate


## Create the storage symbolic link

    $ docker-compose exec webserver php artisan storage:link


## Launching the Web Server

Run the docker containers.

    $ docker-compose up -d

Wait for a few moments for mysql to stabilize.
    
Apache is configured to run on port 80. So, you can access it via `http://dockerhost`


#### Apache Modules

By default following modules are enabled.

* rewrite
* headers

> If you want to enable more modules, just update `./bin/webserver/Dockerfile`. You can also generate a PR and we will merge if seems good for general purpose.
> You have to rebuild the docker image by running `docker-compose build` and restart the docker containers.


#### Connect via SSH

You can connect to web server using `docker-compose exec` command to perform various operation on it. Use below command to login to container via ssh.

```shell
docker-compose exec webserver bash
```


## PHP

The installed version of PHP is 7.1.

You can run php commands like this:

    $ docker-compose exec webserver php artisan migrate


#### Extensions

By default following extensions are installed.

* mysqli
* mbstring
* zip
* intl
* mcrypt
* curl
* json
* iconv
* xml
* xmlrpc
* gd

> If you want to install more extension, just update `./bin/webserver/Dockerfile`. You can also generate a PR and we will merge if seems good for general purpose.
> You have to rebuild the docker image by running `docker-compose build` and restart the docker containers.


## phpMyAdmin

phpMyAdmin is configured to run on port `8080`. Use following default credentials.

http://dockerhost:8080/  
username: root  
password: tiger


## Redis

It comes with Redis. It runs on default port `6379`.
