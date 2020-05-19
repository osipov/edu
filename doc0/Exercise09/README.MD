# Docker Exercise 09

### Getting started with Docker Swarms

Make sure that Swarm is enabled on your Docker Desktop by typing `docker system info`, and looking for a message `Swarm: active` (you might have to scroll up a little).

If Swarm isn't running, simply type `docker swarm init` in a shell prompt to set it up.


### Create the networks:

```
docker network create --driver overlay --subnet=172.10.1.0/24 ex09-frontend

docker network create --driver overlay --subnet=172.10.2.0/23 ex09-backend
```

### Save the MySQL configuration

Save the following to your `development.env` file.

```
MYSQL_USER=sys_admin
MYSQL_PASSWORD=sys_password
MYSQL_ROOT_PASSWORD=root_password
```

### Create your Docker Swarm configuration

```
version: "3"

networks:
  ex09-frontend:
    external: true
  ex09-backend:
    external: true

services:

  ex09-db:
    image: mysql:8.0
    command: --default-authentication-plugin=mysql_native_password
    ports:
      - "3306:3306"
    networks:
      - ex09-backend
    env_file:
      - ./development.env

  ex09-www:
    image: dockerjames85/php-mysqli-apache:1.1
    ports:
      - "8080:80"
    networks:
      - ex09-backend
      - ex09-frontend
    depends_on:
      - ex09-db
    env_file:
      - ./development.env
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 100M
      restart_policy:
        condition: on-failure
```        

### Deploy the stack

```
docker stack deploy -c php-mysqli-apache.yml php-mysqli-apache
```

### Veify the stack has been deployed

```
docker stack ls
```

### Verify all the containers have been deployed

```
docker stack ps php-mysqli-apache
```

### Verify the load balancers have all the replicas and mapped the ports

```
docker stack services php-mysqli-apache
```

### See what containers are on the nodemanager in the swarm

```
docker ps
```

### Verify that the stack is working correctly

```
# local node master
curl http://localhost:8080
```

### Destory and remove the stack
```
docker stack rm php-mysqli-apache
```
