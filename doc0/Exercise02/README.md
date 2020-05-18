# Docker Exercise 02

In this exercise we'll look at the basics of using the command line interface



# Spin up a container
Let's run your first container using a simple command:

```bash
$ docker container run <image name>
```

`<image name>` specifies what image to use to spin up an instance of a container.

An image contains the application to run inside a container.

Let's spin up a container that uses `hello-world:latest` image:

```bash
$ docker container run hello-world:latest
```

You might notice `latest` in the image name and wonder what's that. It's called an image tag, which is used to specify a particular version of an image.

If the tag is not specified with the image name, the `latest` tag is assumed. So if you run the following, the same image will be used:

```bash
$ docker container run hello-world
```

Yay, you just ran your first container!

## So, what happened exactly?
So let's see what happened when you ran the following command:

```bash
$ docker container run hello-world:latest
```

The end result of the above command was tu run a container, but internally, it did the following:

- `docker` command sends the above request to the Docker Daemon, which is running in the background.
- The daemon upon receiving the command, check if the image exists.
- The daemon downloads/pulls the `hello-world:latest` image from a _default_ [registry](/lesson-3) called [Docker Hub](https://hub.docker.com/).
- The daemon created a container with the specified image.
- The daemon streamed the output to the `docker` command which is then displayed on your terminal.

## List the containers
But let's check:

```bash
$ docker container ls
```

Hmm, nothing showed up?

That's because the `hello-world:latest` image contains an application which prints out some text and closes immediately. `docker container ls` only shows currently running containers.

Let's check it again:

```bash
$ docker container ls --all
```

This will show all the containers.

Did you find the one you just created? You'll see a container with `EXITED` status, that's the one.

## Remove a container
Let's remove the container so it looks clean again:

```bash
$ docker container rm <container id>
```

You can get the `<container id>` from `docker container ls --all` command.

## Protip
Docker developers are nice people. They know you'll be using some commands very frequently, so they created some short commands:

| Long command | Short command |
| --- | --- |
| `docker container run <image name>` | `docker run <image name>` |
| `docker container ls` | `docker ps` |
| `docker container rm <container id>` | `docker rm <container id>` |

From now on, we will be using the shorter version of the commands.

### Running an nginx container in background mode and mapping a port

`docker run -d -p 8080:80 nginx`

```
-d=false: Detached mode: Run container in the background, print new container id
-p=[]      : Publish a container's port or a range of ports to the host
               format: ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort | containerPort
               Both hostPort and containerPort can be specified as a
               range of ports. When specifying ranges for both, the
               number of container ports in the range must match the
               number of host ports in the range, for example:
                   -p 1234-1236:1234-1236/tcp

               When specifying a range for hostPort only, the
               containerPort must not be a range.  In this case the
               container port is published somewhere within the
               specified hostPort range. (e.g., `-p 1234-1236:1234/tcp`)

               (use 'docker port' to see the actual mapping)
```

## List the containers
Let's check:

```bash
$ docker container ls
```

This time you should see a container with `nginx` in the name.

Try to remove it:

```bash
$ docker rm <container id>
```

You should see `Error response from daemon` because the container is still running.

Stop the container:

```bash
$ docker stop <container id>
```

Now you can remove it and confirm that it is gone by listing all the containers:

```bash
$ docker rm <container id>
$ docker ps -a
```

This is it! :tada:
