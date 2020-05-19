# Docker Exercise 06

Basic Docker bridge network

### Create your network

```
docker network create mynetwork
```

### Verify your network

```
docker network ls
```

### Create a container within your network

```
docker run -d -p 8080:80 --network mynetwork --name myhost nginx
```

### Create a container using the default network

The image `tutum/curl` is just the base Ubuntu Linux image with `curl` pre-installed.

```
docker run -it tutum/curl bash
```

### Create another container within your network

```
docker run -it --network mynetwork tutum/curl bash
```

### Disconnect your container from your network

```
docker network disconnect mynetwork myhost
```

### Connect your container back to your network

```
docker network disconnect mynetwork myhost
```

# Networked applications

When you deploy your application, you make sure that no unknown service can communicate with it.

For example, you have
- a UI for frontend
- an API server as backend
- a worker as helper to backend
- a database

Now you would not want anyone to access your worker and database directly. In some cases, you would not even want your API server to be exposed as well. So you put them on separate networks.

In Docker, you can create multiple networks and put your containers in one or more networks.

## Create network
Let's create some networks.

```bash
$ for net in $(echo frontend backend db); do \
    docker network create $net;              \
  done
```

The above command will create three different networks for you.

## List networks
You can check them up.

```bash
$ docker network ls
```

## How to connect containers to networks

You may want to implement the following network:

```
    Internet
        |
  [reactjs-app] ---
        |         |--- frontend
  [nodejs-api]  ---
        |         |--- backend
     [worker]   ---
        |         |--- db
    [postgres]  ---
```


Guess what, you are almost there! :tada:

### Resources

* [Docker Reference - Network Create](https://docs.docker.com/engine/reference/commandline/network_create/)
* [Docker Reference - Network Connect](https://docs.docker.com/engine/reference/commandline/network_connect//)
* [Docker Reference - Network Disconnect](https://docs.docker.com/engine/reference/commandline/network_disconnect/)
