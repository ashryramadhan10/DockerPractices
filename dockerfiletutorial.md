# [Dockerfile Tutorial](https://docs.google.com/presentation/d/1tsqkU8bnMHI23X5lBIIiBVf6KPwfEdc2yH4V37a_3sE/edit#slide=id.p)

- [Dockerfile Tutorial](#dockerfile-tutorial)
  - [1. FROM Instruction](#1-from-instruction)
    - [:sparkles: FROM Instruction Example](#sparkles-from-instruction-example)
  - [2. RUN Instruction](#2-run-instruction)
    - [:sparkles: RUN Instruction Example](#sparkles-run-instruction-example)
  - [3. CMD Instruction](#3-cmd-instruction)
    - [:sparkles: CMD Instruction Example](#sparkles-cmd-instruction-example)
  - [4. LABEL Instruction](#4-label-instruction)
  - [5. ADD \& COPY Instructions](#5-add--copy-instructions)
  - [6. .dockerignore file](#6-dockerignore-file)
  - [7. Expose Instruction](#7-expose-instruction)
  - [8. ENV Instruction](#8-env-instruction)
  - [9. VOLUME Instruction](#9-volume-instruction)
  - [10. WORKDIR Instruction](#10-workdir-instruction)
  - [11. USER Instruction](#11-user-instruction)
  - [12. Argument Instruction](#12-argument-instruction)
  - [13. HEALTHCHECK Instruction](#13-healthcheck-instruction)
  - [14. ENTRYPOINT Instruction](#14-entrypoint-instruction)
  - [15. Multi Stage Build](#15-multi-stage-build)
  - [16. Dockerhub Registry](#16-dockerhub-registry)
  - [17. Digital Ocean Container Registry](#17-digital-ocean-container-registry)


## 1. [FROM Instruction](https://docs.docker.com/reference/dockerfile/#from)

`FROM` command is used for creating a build stage from the choosed image.

```dockerfile
FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
```

### :sparkles: FROM Instruction Example

Step-1: Add this to your `Dockerfile`:
```Dockerfile
FROM alpine:3
```

Step-2: Build the dockerfile:
```console
docker build -t practice/from FROM_INSTRUCTION
```

## 2. [RUN Instruction](https://docs.docker.com/reference/dockerfile/#run)

The `RUN` instruction will execute any commands to create a new layer on top of the current image. The added layer is used in the next step in the Dockerfile.

```Dockerfile
# Shell form:
RUN [OPTIONS] <command> ...
# Exec form:
RUN [OPTIONS] [ "<command>", ... ]
```

### :sparkles: RUN Instruction Example
```Dockerfile
FROM alpine:latest

RUN mkdir hello && cd hello
RUN touch hello-world.txt
RUN echo "Hello World" > hello-world.txt && cd ../
```

If you want to see the progress update, you need to set `--progress=plain` or we can use docker cache, if we want to repeat again without cache, we can use command `--no-cache`.

## 3. [CMD Instruction](https://docs.docker.com/reference/dockerfile/#cmd)

The `CMD` command is used for run the container after building process. There will be only **ONE** `CMD` command in a Dockerfile. This command must be put at the end of the script.

```Dockerfile
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
```

### :sparkles: CMD Instruction Example

Here are some examples illustrating different use cases of the `CMD` instruction in a Dockerfile:

1. **Run a Python Script**:
   ```Dockerfile
   CMD ["python", "app.py"]
   ```
   This command specifies that the container should execute the `app.py` Python script when it starts.

2. **Start a Web Server**:
   ```Dockerfile
   CMD ["nginx", "-g", "daemon off;"]
   ```
   This command instructs the container to start the Nginx web server and keep it running in the foreground.

3. **Run a Shell Command**:
   ```Dockerfile
   CMD ["echo", "Hello, world!"]
   ```
   This command prints "Hello, world!" to the container's stdout when it starts.

4. **Run a Custom Command**:
   ```Dockerfile
   CMD ["myscript.sh"]
   ```
   Assuming `myscript.sh` is in the container's filesystem, this command runs the `myscript.sh` shell script when the container starts.

5. **Start a Service**:
   ```Dockerfile
   CMD ["service", "nginx", "start"]
   ```
   This command starts the Nginx service inside the container when it starts.

6. **Override CMD at Runtime**:
   ```Dockerfile
   CMD ["echo", "Default message"]
   ```
   You can override the default command defined in the Dockerfile by providing a new command when running the container:
   ```bash
   docker run my_image echo "Custom message"
   ```

7. **Default Command with Arguments**:
   ```Dockerfile
   CMD ["myapp", "--option", "value"]
   ```
   This command specifies arguments to be passed to the `myapp` executable when the container starts.

8. **Run Multiple Commands**:
   ```Dockerfile
   CMD ["sh", "-c", "echo First command && echo Second command"]
   ```
   This command runs multiple shell commands sequentially when the container starts.

9. **No Command (Override with ENTRYPOINT)**:
   ```Dockerfile
   CMD []
   ```
   This command specifies no default command, allowing the container to be started with a custom command or entrypoint.

In summary, the `CMD` instruction in a Dockerfile specifies the default command to run when a container is started. It can be used to run various types of commands, scripts, services, or executables inside the container.

## 4. [LABEL Instruction](https://docs.docker.com/reference/dockerfile/#label)

The `LABEL` command adds metadata to the image.

```Dockerfile
LABEL <key>=<value> <key>=<value> <key>=<value> ...
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

Check the label using `inspect` command in docker.

```console
docker image inspect <image-name>
```

## 5. [ADD](https://docs.docker.com/reference/dockerfile/#add) & [COPY](https://docs.docker.com/reference/dockerfile/#copy) Instructions

* ADD Instruction:
```Dockerfile
ADD [OPTIONS] <src> ... <dest>
ADD [OPTIONS] ["<src>", ... "<dest>"]

# OPTIONS
--keep-git-dir
--checksum
--chown
--chmod
--link
--exclude
```

* COPY Instruction:
```Dockerfile
COPY [OPTIONS] <src> ... <dest>
COPY [OPTIONS] ["<src>", ... "<dest>"]

# OPTIONS
--from
--chown
--chmod
--link
--parents
--exclude
```

The `COPY` and `ADD` instructions in a Dockerfile are used to copy files or directories from the host machine into the Docker image. While they have similar functionalities, there are some differences between them:

1. **`COPY` Instruction**:
   - The `COPY` instruction copies files or directories from the host machine into the Docker image's filesystem.
   - It is straightforward and designed for copying local files into the image.
   - It doesn't support URL resources or automatic extraction of archives.
   - It is recommended for most use cases where you simply need to copy files or directories into the image.

   Example:
   ```Dockerfile
   COPY ./src /app
   ```

2. **`ADD` Instruction**:
   - The `ADD` instruction also copies files or directories from the host machine into the Docker image's filesystem.
   - In addition to copying local files, `ADD` has some extra features, such as support for URL resources and automatic extraction of archives (e.g., `.tar`, `.tar.gz`, `.zip`).
   - While these additional features can be useful in certain scenarios, they add complexity and potential security risks.
   - Unless you specifically need the extra features provided by `ADD`, it's generally recommended to use `COPY` for simplicity and clarity.

   Example:
   ```Dockerfile
   ADD http://example.com/file.txt /app/file.txt
   ```

In summary:

- Use `COPY` for simple file or directory copying from the host machine into the Docker image.
- Use `ADD` if you need to handle URL resources or automatically extract archives during the copy process. However, be cautious about the added complexity and potential security risks associated with `ADD`.

## 6. .dockerignore file

This file will ignore every files inside of it to be `COPY` or `ADD`. This file support `regex` as well and work very similar to `.gitignore` file.

## 7. Expose Instruction

The `EXPOSE` command tells to us that the container use at what port and using either `tcp` or `udp`, this is just information for the people who are going to use our images.

```Dockerfile
EXPOSE <port-number>/<protocol>
EXPOSE 8080/tcp
```

## 8. ENV Instruction

The `ENV` command is used for change the `environment_variable`. `ENV` variable can be accesses with `${varibale-name}` and can be changed every time when we want to create a container.

```Dockerfile
ENV <variable-name>:<variable-value>
ENV DATA_DIR=/usr/local/var/postgres
COPY /home/user/postgres ${DATA_DIR}

docker run --env <key>=<value> <image-name>
docker run --env DATA_DIR=/usr/local/var/posgres postgres
```

## 9. VOLUME Instruction

This instruction is used for creating `docker volume` using dockerfile command. Every file on the `volume` will be copied to the `Docker Volume` even if we are not creating any Docker Volume while creating the coresponding `Docke Container`. This is appropriate in a use case such saving data inside a file.

* We just need to define which folder we want to save into the volume.
```Dockerfile
VOLUME <path>/<folder>
VOLUME ["path1/folder1", "path2/folder2", ".."]
```

## 10. WORKDIR Instruction

The `WORKDIR` directory is where we determine our working directory (start path directory) to run `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD`. That's mean we determine where to start our Docker Container folder.

```Dockerfile
WORKDIR /app # our workdir is set to /app
WORKDIR Docker # now it becomes /app/Docker
WORKDIR /home/app # now it changes to /home/app
```

```Dockerfile
FROM alpine:3.14

WORKDIR /app
COPY src/hello.txt .
```

## 11. USER Instruction

The `USER` command is used for changes the user of the linux distribution while running the docker image is running.

By default, docker will use `root` user, for sometimes we don't want user `root` runs certain `apps`.

```Dockerfile
USER <user-name>
USER <user-name>:<user-group>
```

```Dockerfile
FROM golang:1.18-alpine

RUN mkdir /app

RUN addgroup -S ashrygroup
RUN adduser -S -D -h /app ashryuser ashrygroup
RUN chown -R ashryuser:ashrygroup /app
USER ashryuser

COPY main.go /app

EXPOSE 8080
CMD go run /app/main.go
```

## 12. Argument Instruction

The `ARG` instruction is used for creating variable during build process. To access the `ARG` varibale, it's same like using `ENV` 

* Build command:
```console
--build-arg <arg-key>=<arg-value>
```

* In Dockerfile:
```Dockerfile
ARG <variable-key>=<variable-value>
```

## 13. HEALTHCHECK Instruction

The `HEALTHCHECK` command is used for checking whether the our Docker Container runs correctly or not. The starting value of the the `HEALTHCHECK` command is `starting`, if the container running successfully it will be changed to `healthy` if not `unhealthy`.

```Dockerfile
HEALTHCHECK NONE
HEALTHCHECK [OPTIONS] CMD
```
OPTIONS:
* --interval=DURATION (default: 30s)
* --timeout=DURATION (default: 30s)
* --start-period=DURATION (default: 0s)
* --retries=N (default: 3)

```Dockerfile
HEALTHCHECK --interval=5s --start-period=5s CMD curl -f http://localhost:8080/health
```

We can see that we set `--interval` to `5s` so that's mean we will run the `CMD` for ever 5 seconds, the start period will be at 5s.

We can check it using `docker container ls` below `STATUS` section.

## 14. ENTRYPOINT Instruction

The `ENTRYPOINT` command is used for determining which executable file our container will run. Usually, `ENTRYPOINT` is strongly related with `CMD` instruction.

```Dockerfile
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT executable param1 param2
```

When we are using `CMD ["param1", "param2"]` the param will be sent to `ENTRYPOINT` executable.

```Dockerfile
FROM golang:1.18-aline

RUN mkdir /app/
COPY main.go /app/

EXPOSE 8080
ENTRYPOINT ["go", "run"]
CMD ["/app/main.go"]
```

## 15. Multi Stage Build

When we create an image from base image that has really big size, our image will have same amount of size with that `base image`.

Therefore, always use only `base image` that we needed for our image.

There are a technique to keep our image small by `compiling` or `build` our necessary/programs first on local then `COPY` it into our `image`.

> Let say we compiled first our `.cpp` programs to `.exe` then just uploading the `compiled` version of our programs, then it will be more space-saving, because we don't need to install `cpp` compiler and compile our `.cpp` inside our `image`.

Docker has a `Multi Stage Build` feature, we can take an advantages this feature for the case above.

> Every `FROM` is a build stage.

```Dockerfile
FROM golang:1.18-alpine as builder
WORKDIR /app/
COPY main.go .
RUN go build -o /app/main main.go

FROM alpine:3
WORKDIR /app/
COPY --from=builder /app/main ./
CMD /app/main
```

## 16. Dockerhub Registry

After we are creating our own images, we can publish our docker images to Dockerhub Registry.

You will need:
* Dockerhub Access Token
* Docker login using: `docker login -u <username>`
* Docker push: `docker push <registry-name>/<image-name>`

## 17. Digital Ocean Container Registry

This is one of the popular cloud provider for container registry. You just need to set the `Docker Config`, it's different from Dockerhub that need login credential to push our docker image.