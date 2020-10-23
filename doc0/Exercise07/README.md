# Docker Exercise 07

# Docker Compose
Just like `Dockerfile` makes creating images easier, Docker Compose makes deploying containers easier.

Docker Compose expects a YAML file, usually named `docker-compose.yml`, which contains information about what image to use to run containers, ports to expose, volumes to mount, networks, etc.

It let you run all of the above as single deployment, yet be scalable at the same time.

## Example Voting App
Docker has open-sourced a very good example of a web app and uses `docker-compose.yml` to deploy it.

https://github.com/dockersamples/example-voting-app

Read the [Architecture](https://github.com/dockersamples/example-voting-app#architecture) from the above repository description and come back.

Ready? Let's open up the `docker-compose.yml` file.

The file is pretty explainatory, but from a higher level, this is the format:

```yaml
version: "3"

services:
  [container 1 name]:
    [fixed image or build method]
    [ports to expose]
    [volumes to mount]
    [networks to mount]

  ...

volumes:
  [volume 1]:
    [mount mode and options]

  ...

networks:
  [network 1]:
    [mount mode and options]

  ...

...
```

Spin up the complete application.

```bash
$ docker-compose up
```

Now check all the running containers, or inspect each container for more details.

```bash
$ docker inspect <container id>
```

### Create your own Docker compose configuration

Start by creating a Dockerfile for a PHP server using the following commands.

```
FROM php:7.2-apache
```

Let's add xdebug to our installation

```
RUN pecl install xdebug-2.6.0
```

Enable the module

```
RUN docker-php-ext-enable xdebug
```

Let's also add redis

```
RUN pecl install redis-4.0.1
```

And enable the redis module

```
RUN docker-php-ext-enable redis
```

Finally, let's set an environment variable for Redis

```
ENV REDIS_HOST redis
```

### Create our docker compose file

Save the following to `docker-compose.yaml`.

```
version: "3"
services:
    www:
        build: .
        ports:
            - "8080:80"
        volumes:
            - ./src:/var/www/html/
        networks:
            - default
```

### Start the Docker container

```
docker-compose up
```

### Stop and remove the Docker container

```
docker-compose down
```

That's all folks! :tada:

### Resources

* [Docker Compose](https://docs.docker.com/compose/)
* [Docker Compose Reference - up](https://docs.docker.com/compose/reference/up/)
* [Docker Compose Reference - down](https://docs.docker.com/compose/reference/down/)
