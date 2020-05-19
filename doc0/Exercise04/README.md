# Images
OK, first of all, it's not `JPG` or `PNG` file that we're talking about here. It's important to learn a little about images because that's what will be used to run containers

Docker Images are just like `tar`/`zip` files. A Docker Image contains everything to setup a container.

We'll learn about how to create images in a later lesson, but for now, let's see what image name tells us.

## Tags
An image name is composed of two parts:

```
[image name]:[tag]
hello-world:latest
```

Tag is used to label an image with different version.

Suppose, I have an image that contains `v1.0` of my web server, I could tag it as `myserver:v1.0`.

Different tags can point to the same image. In the above case, the same image can also be tagged as `myserver:latest`, which will be pointing to the `myserver:v1.0` image.

In future, I can create another version of the image and tag it as `myserver:v2.0` and point `myserver:latest` to the newer image.

# Registry
Registry is a place where you can upload and reuse the images uploaded by other users.

[Docker Hub](https://hub.docker.com/) is a registry maintained by Docker itself. You can upload your own images, or find all the official images from there, even the `hello-world:latest` image that we used earlier.

Docker has a command to search for images through CLI, for example:
```bash
docker search nginx
```

## Pull an image
In the earlier exercise when we ran, `docker run hello-world:latest`, it pulled the image itself. But it is possible to pull an image beforehand to save time:
```bash
docker pull <image name>
```

It will take some time to pull, depending on the size of the image.

## Repository name
Repository name comes in when you pull an image uploaded by some user.

```
[repo name]/[image name]:[tag]
quay.io/ukhomeofficedigital/nginx-proxy:latest
```

This represents that `quay.io/ukhomeofficedigital` is the owner of the image `nginx-proxy:latest`.

You can pull the above image like this:

```bash
$ docker pull quay.io/ukhomeofficedigital/nginx-proxy:latest
```

In case of, `hello-world:latest`, there is no repository name. That shows the image is owned/maintained by Docker Hub.

It is recommended that you use images that are maintained by Docker Hub. Some images maintained by them are listed below as examples:

- `hello-world:latest`
- `nginx:latest`
- `node:latest`
- `ubuntu:latest`

## List the images
Let's see where does it show up after its pulled:

```bash
$ docker image ls
```

The list will show you all the images, with their tags and the time when they were updated.

## Retagging Images
You can retag an image with a different name.

```bash
$ docker <old image name> <new image name>
```

Suppose, you wish to upload a copy of `hello-world:latest` image on your repository.

You'll first retag it.

```bash
$ docker tag hello-world:latest <your DockerHub username>/hello-world:latest
```

It will give you an error if you push the image that you're not logged in. You'll first need to create an account on [Docker Hub](https://hub.docker.com/) and login.

```bash
$ docker login
```

And then push it to the registry.

```bash
$ docker push <your DockerHub username>/hello-world:latest
```

## Remove an image
Sometimes you've pulled so many images that your disk run out of space. So you should remove all the unwanted images:

```bash
$ docker rmi <image name>
```

`rmi` stands for `remove image`.


Congrats, you just used DockerHub registry! :tada:
