![Basic Docker Commands](https://camo.githubusercontent.com/3899e4bc368d2015dd35b7dc92b081bb044a9ce0/68747470733a2f2f7777772e746872656174737461636b2e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031372f30362f646f636b65722d636c6f75642d747769747465722d636172642e706e67)

***

<p align="center">
Some basic docker commands often used in working routine along with its options
</p>
<p align="center">For quick reference</p>

***


🐳 Introduction
=================

Here is a list of often used Docker commands for quick reference, with some usage examples. Have in mind that if your user doesn't have root rights, you must precede the commands with **sudo**.

I've splitted the commands through the following topics:

* [Working with containers](#-working-with-containers)
* [Working with images](#-working-with-images)
* [File descriptor - Dockerfile Example](#-file-descriptor---dockerfile---example)
* [Working with networks](#-working-with-networks)
* [Docker Compose File Example](#-docker-compose-file-example)
* [Docker Compose](#-docker-compose)


📦 Working with containers
--------------
Run (create, start and execute) a command in a new container
```
$ docker container run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
Options | Description
------- | -------
-d | Detach, run container in background
-it | Interactive, access container's terminal
--name [NAME] | Assign a name to the container
--net [NETWORK] | Connect container to a network
-p | Publish container’s port(s) to the host
--rm | Automatically remove the container when it exits
-v | Bind mount a volume
--volumes-from=[CONTAINER(S)] | Mount volumes from the specified container(s)

List containers
```
$ docker container ls [OPTIONS]
```
Options | Description
------- | -------
-a | Show all containers (default shows just running)

Start container(s)
```
$ docker container start [OPTIONS] CONTAINER [CONTAINER...]
```
Options | Description
------- | -------
-ai | Attach container's terminal, interactive

### Usage examples

Map container's ports
```
$ docker container run -p [HOST_PORT]:[CONTAINER_PORT] CONTAINER
```

Map volume
```
$ docker container run -v [HOST_VOLUME]:[CONTAINER_VOLUME] CONTAINER
```

Run container in background
```
$ docker container run -d CONTAINER
```

Stop running container
```
$ docker container stop CONTAINER
```

Run a command in a running container
```
$ docker container exec CONTAINER COMMAND [ARG...]
```

Fetch the logs of a container
```
$ docker container logs CONTAINER
```

Display detailed information on one or more container
```
$ docker container inspect CONTAINER [CONTAINER...]
```

💽 Working with images
--------------
List images
```
$ docker image ls
```
Download image from registry
```
$ docker image pull IMAGE_NAME[:IMAGE_TAG]
```
Display detailed information on one or more images
```
$ docker image inspect IMAGE [IMAGE...]
```
Create image (copy) with different tag
```
$ docker image tag SOURCE_IMAGE:TAG TARGET_TAG
```
Create image (copy) with different name and tag
```
$ docker image tag SOURCE_IMAGE TARGET_IMAGE:TARGET_TAG
```
Remove image
```
$ docker image rm IMAGE_NAME[:IMAGE_TAG]
```
Build an image from a Dockerfile
```
$ docker image build [OPTIONS] DOCKERFILE_PATH
```
Options | Description
------- | -------
--build-arg [ARG]=[VALUE] | Set build-time variables
-t | Name and optionally a tag in the 'name:tag' format

Log on Docker Hub registry
```
$ docker login --username=[USERNAME]
```
Push image on registry
```
$ docker image push IMAGE_NAME[:IMAGE_TAG]
```

📄 File descriptor - Dockerfile - Example
--------------
```
FROM IMAGE_NAME[:IMAGE_TAG]
RUN [COMMAND] && \
    [OTHER_COMMAND]
LABEL
    maintainer '[IMAGE_MAINTAINER]'
ARG [FOO]=[DEFAULT_VALUE]
ENV [VAR]=$FOO (refering to previous arg definition)
COPY [HOST_SOURCE_FILE_PATH] [IMAGE_TARGET_FILE_PATH]

USER [USER]  (user active in container)
VOLUME [PATH]
WORKDIR [PATH]
EXPOSE [PORT]

ENTRYPOINT [ENTRYPOINT]
CMD [COMMAND TO BE PASSED TO ENTRYPOINT]
```

🌎 Working with networks
--------------
List networks
```
$ docker network ls
```
Display detailed information on one or more networks
```
$ docker network inspect NETWORK [NETWORK...]
```
Create a network
```
$ docker network create [OPTIONS] NETWORK
```
Options | Description
------- | -------
--driver [none|bridge|host] | Driver to manage the Network

Connect a container to a network
```
$ docker network connect NETWORK CONTAINER
```
Disconnect a container from a network
```
$ docker network disconnect NETWORK CONTAINER
```

📃 Docker Compose File Example
--------------
docker-compose.yml
```yml
version: '2'
volumes:
    vdata:
networks:
    ndatabase:
    nweb:
    nqueue:
services:
    db:
        image: postgres:9.6
        networks:
            - ndatabase
        volumes:
            # Data volume
            - vdata:/var/lib/postgresql/data
            # Scripts
            - ./scripts:/scripts
            - ./scripts/init.sql:/docker-entrypoint-initdb.d/init.sql
    frontend:
        image: nginx:1.13
        networks:
            - nweb
        depends_on:
            - app
        volumes:
            # Site
            - ./web:/usr/share/nginx/html/
            # Reverse proxy config
            - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
        ports:
            - 8000:80
    app:
        image: python:3.6
        networks:
            - ndatabase
            - nweb
            - nqueue
        depends_on:
            - db
            - queue
        environment:
            - DB_NAME=email_sender
        volumes:
            # Application
            - ./app:/app
        working_dir: /app
        command: bash ./app.sh
    queue:
        image: redis:3.2
        networks:
            - nqueue
    worker:
        build: worker
        networks:
            - nqueue
        depends_on:
            - queue
        volumes:
            # Worker
            - ./worker:/worker
        working_dir: /worker
        command: worker.py
```

🐙 Docker Compose
--------------
cd to docker-compose.yml folder...

Builds, (re)creates, starts, and attaches to containers for a service
```
$ docker-compose up [options] [--scale SERVICE=NUM] [SERVICE...]
```
Options | Description
------- | -------
-d | Detached mode: Run containers in the background

List containers
```
$ docker-compose ps
```
Run arbitrary commands in service
```
$ docker-compose exec SERVICE COMMAND [ARGS...]
```
Stop containers and removes containers, networks, volumes, and images created by up
```
$ docker-compose down
```
Display log output from services
```
$ docker-compose logs [options] [SERVICE...]
```
Options | Description
------- | -------
-f | Follow log output
-t | Show timestamps


## 👥 Contribution
Feel free to
- Report issues
- Open pull request with improvements
- Spread the word
