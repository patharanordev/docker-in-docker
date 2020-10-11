# Docker-in-Docker (dind)

Ref. https://hub.docker.com/_/docker

> Starting in 18.09+, the dind variants of this image will automatically generate TLS certificates in the directory specified by the DOCKER_TLS_CERTDIR environment variable.

```bash
$ (docker network create dind-network || :) && \
docker run --privileged --name dind -d \
    --network dind-network \
    -e DOCKER_TLS_CERTDIR=/certs \
    -v $(pwd)/certs/ca:/certs/ca \
    -v $(pwd)/certs/client:/certs/client \
    docker:dind

CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                                            NAMES
09........26        docker:dind                 "dockerd-entrypoint.…"   11 seconds ago      Up 6 seconds        2375-2376/tcp                                    dind
```

Checking docker version of docker inside the docker !!! :

```bash
$ docker exec dind docker version
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46
 Built:             Wed Sep DD HH:mm:ss YYYY
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.13
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       44...46
  Built:            Wed Sep DD HH:mm:ss YYYY
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.3.7
  GitCommit:        8fb..................................175
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc....................................dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fe...83
```

## Docker Compose

Just:

```bash
$ docker-compose -f docker-compose.yml up --build -d

Creating network "docker-in-docker_default" with the default driver
Creating dind ... done
```

## Where to Store Data

Create a data directory on a suitable volume on your host system, e.g. `/my/own/var-lib-docker`.

Start your docker container like this:

```bash
$ docker run --privileged --name some-docker -v /my/own/var-lib-docker:/var/lib/docker -d docker:dind
```

The `-v /my/own/var-lib-docker:/var/lib/docker` part of the command mounts the `/my/own/var-lib-docker` directory from the underlying host system as `/var/lib/docker` inside the container, where Docker by default will write its data files.

## License

MIT