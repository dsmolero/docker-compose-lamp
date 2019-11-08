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

cd into the directory of a Laravel project:

    $ cd /var/www/mylaravelproject/

Clone this repository and switch to branch `7.1.x`. 

    $ git clone https://github.com/dsmolero/docker-compose-lamp.git

Now your Laravel project directory should resemble this:

    ex:
        /var/www/mylaravelproject/
            app/
            public/
            component.json
            ...
            docker-compose-lamp/
            ...

> Note: There is NO need to set permissions for `./storage` and `./bootstrap/cache` to `_www` or `www-data`

Checkout the PHP version that you need:

    ```shell
    $ cd docker-compose-lamp/
    $ git fetch --all
    $ git checkout 7.1.x
    $ cp sample.env docker.env
    ```

Launch the container:

    $ docker-compose -f docker-compose-lamp/docker-compose.yml up -d

Wait a few moments for the mysql database server to stabilize.

Your LAMP stack is now ready!! You can access it at `http://dockerhost`.


## Configure to Integrate your Laravel Application

This package comes with default configuration options. To customize:

Shut down the docker containers:

    $ docker-compose -f docker-compose-lamp/docker-compose.yml down

Append the contents of `sample.env` into your project's `.env` file:

    $ cat docker-compose-lamp/sample.env >> .env

Edit your `.env` file (the new settings are at the bottom):

    $ nano .env

Modify the following entries:

    PROJECT_ROOT=..
    # You can leave the following to their defaults
    DB_CONNECTION=mysql
    DB_HOST=mysql
    DB_PORT=3306
    DB_DATABASE=myapp
    DB_USERNAME=root
    DB_PASSWORD=tiger


### Configuration Variables

There are following configuration variables available and you can customize them by overwritting in your own `.env` file.

_**DCL_PROJECT_ROOT**_

The project directory. The document root for Apache server will be ${PROJECT_ROOT}/public. Set this to `..`. The default value for this is `./www`.

_**DCL_VHOSTS_DIR**_

This is for virtual hosts. The default value for this is `./config/vhosts`. You can place your virtual hosts conf files here.

> Make sure you add an entry to your system's `hosts` file for each virtual host.

_**DCL_PHP_INI**_

Path to php.ini file. The default is at ./config/php/php.ini

_**DCL_APACHE_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/apache2`.

_**DCL_MYSQL_LOG_DIR**_

This will be used to store Apache logs. The default value for this is `./logs/mysql`.

_**DCL_MYSQL_ROOT_PASSWORD**_

This will be the password for user root of the MySQL database server. The default value is 'tiger'.


## Launching the Web Server

Run the docker containers.

    $ docker-compose up -d

> We are not ready to visit the website yet. We still have to prepare the database.

Wait for a few moments for mysql to stabilize.
    

## Create the Application Database
    
Open phpmyadmin in your browser
  
    http://dockerhost:8080

Create the database for your app
  
    myapp

Do the database migrations. In the terminal:

    $ docker-compose -f docker-compose-lamp/docker-compose.yml exec webserver php artisan migrate


## Create the storage symbolic link:

    $ docker-compose -f docker-compose-lamp/docker-compose.yml exec webserver php artisan storage:link


## Ready!

Apache is configured to run on port 80. So, you can access it via `http://dockerhost`.


#### Apache Modules

By default following modules are enabled.

* rewrite
* headers

> If you want to enable more modules, just update `./bin/webserver/Dockerfile`. You can also generate a PR and we will merge if seems good for general purpose.
> You have to rebuild the docker image by running `docker-compose build` (from within this project's directory) and restart the docker containers.


#### Connect via SSH

You can connect to web server to perform various operations on it. Use below command to login to container via ssh.

    $ docker-compose -f docker-compose-lamp/docker-compose.yml exec webserver bash


## PHP

The installed version of PHP is 7.1.

You can run php commands like this:

    $ docker-compose -f docker-compose-lamp/docker-compose.yml exec webserver php artisan route:list


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
> You have to rebuild the docker image by running `docker-compose build` (from within this project's directory) and restart the docker containers.


## phpMyAdmin

phpMyAdmin is configured to run on port 8080. Use following default credentials.

http://dockerhost:8080/  
username: root  
password: tiger


## Redis

It comes with Redis. It runs on default port `6379`.
