# DockerPractices
Docker Practices

## 1.1. Docker Commands

### 1.1.1. Build Command

> -t: tag
> hello-docker: the tag/name

```console
docker build -t hello-docker
```

### 1.1.2. Check Image List

```console
docker image ls
```

### 1.1.3. Run Docker Image

```console
docker run hello-docker
```

> for interactive mode:

```console
docker run -it ubuntu
exit
```

> for run on background/detached:

```console
docker run -d postgree
```

> for named containers:

```console
docker run --name <container-name> <image-name>
docker stop <container-name>
```

### 1.1.4. Pull Image

```console
docker pull <image-name>
```

### 1.1.5. Check/Filtering Containers

```console
docker ps -a
```

> for filtering:

```console
docker ps -f "name=<container-name>"
```

### 1.1.6. Container Logs

```console
docker logs <container-id>
```

> for real-time logs:

```console
docker logs <container-name>
```

### 1.1.7. Cleaning Up Container

```console
docker stop <container-id>
docker container rm <container-id>4
```
