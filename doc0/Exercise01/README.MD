# Docker Exercise 01

In this exercise we'll look at the basics of running containers

### Verify the docker install

Once installed, check if Docker is working properly:
```bash
$ sudo docker info
```

Don't worry if you are overwhelmed by the details in the output. You are just starting out :)

### HINT

If you have to use `sudo` for all Docker commands. It can become painful very quickly when you have to frequently enter Docker commands. To remedy this, you can add your current user `$(whoami)`, to `docker` group:

```bash
$ usermod -aG docker $(whoami)
```

Once this is done, you may need to restart you terminal so that the above change can take affect.

If you have logged in to a machine via SSH, just close the SSH session and login again.

To make sure that the user has been added to `docker` group, run the following command and expect no errors:

```bash
$ docker info
```

### Try running a hello world container

`docker run hello-world`

### Show the running containers (if any)

`docker ps`

### Stopping a running container

`docker stop CONTAINER_ID`

Voila, you just installed Docker! :tada:

### Resources

* [Docker Reference - run](https://docs.docker.com/engine/reference/run/)
* [Docker Reference - ps](https://docs.docker.com/engine/reference/commandline/ps/)
* [Docker Reference - stop](https://docs.docker.com/engine/reference/commandline/stop/)
