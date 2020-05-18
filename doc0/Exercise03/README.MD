# Docker Exercise 03

This exercise we're going to look at building our own images from a Dockerfile

### Create a file named `Dockerfile` with the following contents

```
FROM nginx
COPY html /usr/share/nginx/html
```

### Create a directory named `html`

In the `html` directory place a file named `hello.html` with the following contents

```
<html>
  <body>Hello Docker World</body>
</html>
```

### Create our Docker image from our Dockerfile

`docker build --tag my_http .`

### Check that our image has been created

`docker images`

### Run our docker image on port 8080

`docker run -d -p 8080:80 my_http`

### Navigate to `hello.html`

Using your browser open `localhost:8080/hello.html` and confirm that you see the `Hello Docker World` message.

### Stop the container

`docker stop CONTAINER_ID`

### Find the image ID

`docker images`

### Remove the image

`docker rmi my_http`

### If removal fails force the removal

`docker rmi my_http --force`

### Resources

* [Docker Reference - builder](https://docs.docker.com/engine/reference/builder/)
* [Docker Reference - build](https://docs.docker.com/engine/reference/commandline/build/)
* [Docker Reference - images](https://docs.docker.com/engine/reference/commandline/images/)
* [Docker Reference - rmi](https://docs.docker.com/engine/reference/commandline/rmi/)
