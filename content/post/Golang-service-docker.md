---
title: Two ways of creating a Go (golang) Docker container
description: Differents approach to create a Docker container of Golang products.
date: 2015-10-28
draft: true
Keywords:
- Docker
- Golang
- Go
- Development
- Community
Tags:
- Development
- Go
- Docker
- Community
Topics:
- Development
---

![](/media/docker-golang.png "Docker + Golang == Love")

Just for fun & learn I developed a pair of bots for telegram in golang. I wrote a bit about in [this post](post/blabla). Feel free to read it and comment about it :-)

I was interested on dig in Go and I though it was a fun way to do it. 

We have two ways to do it:

1. First: using a stock image and adding our golang application (a waste of space) - Straightforward:
2. Second: try to optimize this

I will use the [gfxBot](https://github.com/gloob/gfxBot) code as reference in all the article.

## First Option (more storage size)

### Definition

Seems we can just define to use an official golang image from docker hub, define a basic Dockerfile and make it play.

This is defined here https://blog.golang.org/docker

Specially interesting the :onbuild directive that makes all the process very easy.

This is the content of the Dockerfile of Golang 1.5

```
FROM golang:1.5

RUN mkdir -p /go/src/app
WORKDIR /go/src/app

# this will ideally be built by the ONBUILD below ;)
CMD ["go-wrapper", "run"]

ONBUILD COPY . /go/src/app
ONBUILD RUN go-wrapper download
ONBUILD RUN go-wrapper install   
```

So, we are talking about a file with just this line:

```
FROM golang:onbuild
```

### Image creation

We need first get the code, you can clone it from the [project's github page](https://github.com/gloob/gfxBot).

and build the Docker image

```
$ docker build -t gfxbot .
```

and run it

```
$ docker run --name gfxbot_main --rm gfxbot
```

We can check that the virtual size of the image is ~ 760MB

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
gfxbot              latest              e50ac4e6fa82        25 seconds ago      764.1 MB
```


### Configuration

This is the simplest example to run a golang project into docker. But for make GfxBot run successfully we need to inject into the container the configuration of the bot (Credentials, etc...), let's see how we can do that with Docker.

@TODO: Make a private repository containing setup (bitbucket?) and adding into the container.

Finally I decided to copy from the host machine into the Docker image the configuration because it's the easiest way. In the future I will add a configuration manager (ansible, chef) but at the moment it seems overkill to use it for this. @TODO: Consul/etcd/Vault?

So, we need to copy the current configuration defined in the host into the container.

There's a shortcut from Docker >= 1.8 that is cp command, it can be used like:

```
$ docker cp ~/.config/irc_bot gfxbot_main:~/.config/
```

although in my case is not working, giving this error:

```
Error response from daemon: lstat /var/lib/docker/devicemapper/mnt/45571d395207cc6d565b8860287f80734c774f9dcbb260e653f7862b85a3f46b/rootfs/~: no such file or directory
```

So I will use a direct workaround for this.

```
$ docker exec -i gfxbot_main bash -c 'mkdir -p ~/.config/gfxbot && cat > ~/.config/gfxbot/config.toml' < ~/.config/gfxBot/config.toml
```

And will commit this change into a new image to use it for production.

```
$ docker commit `docker ps -l -q` gfxbot_conf
```

and run our main bot server with the image created with the config in the last step.

```
$ docker run --name gfxbot_main --rm gfxbot_conf
```


## Second Option (less storage size)

There's other alternative to have a little images (around 1% size of the first option).

TODO: Add this other way.

## Conclusion & Disclaimer

Yeah, Application containers shouldn't store application data as make difficult the updating of a new version.

## Reference

1. Good article about [how to do small golang docker images.](http://www.iron.io/blog/2015/07/an-easier-way-to-create-tiny-golang-docker-images.html)
3. Use a Makefile, a simple Docker Image and Go’s power. [Go, Docker workflow](http://blog.crowdpatent.com/a-go-docker-workflow/) 
4. It's really good (although some time confusing the linking schema) the [Docker documentation](https://docs.docker.com/userguide/usingdocker/).
