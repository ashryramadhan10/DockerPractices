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