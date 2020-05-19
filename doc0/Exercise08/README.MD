# Docker Exercise 08

Docker Networking in practice with PHP & MySQL

Start by creating a new working directory.

In the working directory, create two empty sub-directories: `db` and `www`.

### Create our database Dockerfile in `./db`

```
FROM mysql:8.0
```

### Create our web Dockerfile in `./www`

```
FROM php:7.2-apache

RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli

RUN docker-php-ext-install xdebug
RUN docker-php-ext-enable xdebug
```

### Setup our network in the Docker compose file

```
version: "3"

networks:
  ex08-frontend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.10.1.0/24
  ex08-backend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.10.2.0/23
```

### Create a database environment file

Save the following to a file named `development.env`

```
MYSQL_USER=sys_admin
MYSQL_PASSWORD=sys_password
MYSQL_ROOT_PASSWORD=root_password
```

### Add the database container

```
services:
  ex08-db:
    build: ./db
    command: --default-authentication-plugin=mysql_native_password
    ports:
      - 3306:3306
    networks:
      ex08-backend:
        ipv4_address: 172.10.3.2
    env_file:
      - ./development.env
```

### Add the web container

```
  ex08-www:
    build: ./www
    ports:
      - 8080:80
    volumes:
      - ./src:/var/www/html/
    networks:
      ex08-backend:
        ipv4_address: 172.10.2.2
      ex08-frontend:
        ipv4_address: 172.10.1.2
    depends_on:
      - ex08-db
    env_file:
      - ./development.env
```

### PHP Script to test the networking

```
<?php

$mysqli = new mysqli('ex08-db', getenv('MYSQL_USER'), getenv('MYSQL_PASSWORD'), 'information_schema');

if ($mysqli->connect_error) {
    echo 'Connection Error [', $mysqli->connect_errno, ']: ', $mysqli->connect_error;
} else {
    echo 'MySQLi Connected Successfully!';
}
```

### Start the containers, networks and volumes

```
docker-compose up
```

### To destroy the setup

```
docker-compose down
```

### Resources

* [Docker - Network](https://docs.docker.com/network/)
* [Docker - Environment Variables](https://docs.docker.com/compose/environment-variables/)
