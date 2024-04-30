# DockerPractices
Docker Practices

## 1.1. Docker Commands

### 1.1.1. Build Command

> -t: tag
> hello-docker: the tag/name

```console
docker build -t hello-docker .
```

### 1.1.2. Check Image List

```console
docker image ls
```

### 1.1.3. Run Docker Image

```console
docker run hello-docker
```

```console
docker container create --name <container-name> <image-name>
docker container start <container-name>
docker container exec <container-name> </bin/bash/script>
```

> for interactive mode:

```console
docker container exec -i -t ubuntu /bin/bash
# atau
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
docker stop <container-name> / docker container stop <container-name>
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
docker container rm <container-id>
```

## 1.2. Managing Local Docker Images

### 1.2.1. Pulling an Image

> for pulling an image:
```console
dokcer pull <image-name>:<image-version (optional) >
```

### 1.2.2. Listing Images

> for listing images:
```console
docker images
```

```console
docker images --filter "reference=<repository-name>"
```

### 1.2.3. Removing Images

> for removing images:
```console
docker image rm <image-name>
```

> for remove all stopped containers:
```console
docker container prune
```

> for remove all unused images
```console
docker image prune -a
```

## 1.3. Distributing Docker Images

### 1.3.1. Private Docker Registries

* Names start with the url of the private registry:
```console
dockerhub.myprivateregistry.com/classify_spam
```
* Pull the image:
```console
docker pull dockerhub.myprivateregistry.com/classify_spam:v1
```

### 1.3.2. Pushing to a Registry

```console
docker image push <image-name>
```

* For pushing to a specific registry -> name of the image need to start with the registry url:
```shell
# give tag to the image first
docker tag classify_spam:v1 dockerhub.myprivateregistry.com/classify_spam:v1

# push to dockerhub
docker image push dockerhub.myprivateregistry.com/classify_spam:v1
```

### 1.3.3. Authenticating Against Registry

```shell
docker login dockerhub.myprivateregistry.com/classify_spam:v1
```

### 1.3.4. Send to Docker Images as a File

To send a Docker Image to a few people we can use a Docker as a file.

```shell
# for saving the image file
docker save -o image.tar classify_spam:v1

# for loading the image file
docker load -i image.tar
```

## 1.4. Creating Your Own Docker Image

### 1.4.1. Starting a docker file

A Docker file always starting from another image, specified using the `FROM` instruction.

```dockerfile
FROM postgres:15.0
FROM ubuntu:22.04
FROM hello-world:latest
FROM my-custom-data-pipline:v1
```

### 1.4.2. Building a docker file

Building a docker file create an image.

```console
docker build /location/to/DockerFile
docker build .
docker build -t my_image:v0 .
```

### 1.4.3. Customizing Images

```dockerfile
RUN <valid-shell-command>
```

```dockerfile
FROM ubuntu:22.04
RUN apt-get-update
RUN apt-get install -y python3
```

* Note* use `-y` to avoid any prompts

### 1.4.4. Managing Files in Your Image

* `COPY`ing files to an image, the `COPY` instruction copies files from our local machine into the image we're building.
```Dockerfile
COPY <src-path-on-host> <dst-path-on-image>
COPY /projects/pipeline_v3/pipeline.py /app/pipeline.py
```

* Not specifying a filename in the src-path will copy all the file contents.

```Dockerfile
COPY <src-folder> <dest-folder>
COPY /projects/pipeline_v3/ /app/
```

* Downloading files, instead of copying files from a local directory, files are often downloaded in the image build.
```Dockerfile
# Download the file
RUN curl <file-url> -o <destination>

# Unzip the file
RUN unzip <dest-folder>/<filename>.zip

# Remove the original zip file
RUN rm <copy_directory>/<filename>.zip
```

* Downloading files efficiently 
```Dockerfile
# 1. Each instruction that downloads files adds to the total size of the image.
# 2. Even if the files are later deleted.
# 3. The solution is to download, unpack and remove files in a single instruction.

RUN curl <file_download_url> -o <destination_directory>/<filename>.zip \ 
&& unzip <destination_directory>/<filename>.zip \ 
&& rm <destination_directory>/<filename>.zip
```

## 1.5. Choosing a Start Command for Your Image

Using `CMD` command to execute shell command.

Starting an image:
```console
docker run <image>
```

Starting an image with custom start command:
```console
docker run <image> <shell-command>
```

Starting an image interactively with custom start command:
```console
docker run -it <image> <shell-command>
```

## 1.6. Docker Caching

Docker make layers when everytime we build it. These layers will be saved or cached to make a faster build, so the best practice is to make the order of your Dockerfile script correctly, the one that frequently changing must be put at the bottom of your script, so docker build will be more faster at the first start.

## 1.7. Changing Users and Working Directory

`WORKDIR` and `USER` command.

```Dockerfile
FROM ubuntu:22.04
RUN useradd -m repl
USER repl
RUN mkdir -p /home/repl/projects/pipeline_final
COPY /home/repl/project /home/repl/projects/pipeline_final
```

* Using `WORKDIR`:
```Dockerfile
FROM ubuntu:22.04
RUN useradd -m repl
USER repl
WORKDIR /home/repl/
RUN mkdir -p projects/pipeline_final
COPY /home/repl/project projects/pipeline_final
```

## 1.8. Variables in Dockerfile

* Using `ARG` command:
```Dockerfile
# ARG <variable-name>=<variable-value>
FROM ubuntu
ARG python_version=3.9.7-1+bionic1
RUN apt-get install -y python3=$python_version
RUN apt-get install -y python3-dev=$python_version
```

* Setting `ARG` at build time:
```Dockerfile
FROM ubuntu
ARG project_folder=/projects/pipeline_v3
COPY /local/project/files $project_folder
COPY /local/project/test_files $project_folder/tests
```
On terminal:
```console
docker build --build-arg project_folder=/repl/pipeline .
```

* Using `ENV` command, still accessible in runtime:
```Dockerfile
# ENV <variable-name>=<variable-value>
ENV DB_USER=pipeline_user
```

To use in Dockerfile or at runtime `$DB_USER`, for example:
```Dockerfile
CMD psql -U $DB_USER
```

* Setting a directory to be used at runtime:
```Dockerfile
ENV DATA_DIR=/usr/local/var/postgres
ENV MODE production
```

* Setting or replacing varibale at runtime:
```console
docker run --env <key>=<value> <image-name>
docker run --env POSTGRES_USER=test_db --env POSTGRES_PASSWORD=test_db postgres
```

## 1.9. Container Resource Limit

```console
docker container stats
```

## 1.10. Docker Container Resource Limit

* When we are creating the container we need to specify the `--memory` argument, and followed by `k` (kilo), `m` (mega), `g` (giga), example: `--memory 1g`
* `--cpu` argument, example: `--cpu 1.5` it will be used 1 half of our cpu

```console
docker container create --name smallnginx --publish 8080:80 --memory 100m --cpu 0.5 nginx:stable-perl
docker container start smallnginx
docker container stats
```

## 1.11. Container Port

* We know that the container is isolated inside the Docker
* Our System Host (our PC) cannot access the container directly, the only way to do this is by using `container exec` or `docker run` commands
* Usually the a container run on a certain port, take an example redis run on port 6379, we can see the list of which port that our containers used to run its environments

> To do this Docker use Port Forwarding technique, to forward from host port to open the container port

```console
docker container create --name <container-name> --publish <host-port>:<container-port> <image-name>:<image-tag>
docker container create --name mynginx --publish 8080:80 nginx:latest
```

## 1.12. Bind Mount

Bind mount is a technique to bind the our local storage with the container storage, let say you want to save the mongodb data from your mongodb container to your local, thus if you delete your container, the data will remain on your local storage. Example:

```console
docker container create --name <container-name> --mount "type=bind,source=<src-folder-path>,destination=<dst-folder-path,readonly>" <image-name>:<image-tag>
```

```console
docker container create --name mongodata --mount "type=bind,source=/home/webscientia/mongo-data,destination=/data/db" --publish 27018:27017 --env MONGO_INITDB_ROOT_USERNAME=ashry --env MONGO_INITDB_ROOT_PASSWORD=ashry mongo:latest
```

## 1.13. Docker Volume

Docker volume works similar to docker bind mount, but it's not using our local storage instead it will using the docker volume type.

* Before we need to save our data to volume, we need to create the docker volume first using the command:

```console
docker volume create <volume-name>
docker volume ls
```

`Note*`: 

> Same as container we can't delete the image related to it if the container is running.

> the Docker Volume we can't delete volume if it still used by the container.

* How to delete the Docker Volume:

```console
docker volume rm <volume-name>
```

* How to use the volume with cointainer:

```console
docker container create --name mongovolume --mount "type=volume,source=<volume-name>,destination=/data/db" --publish 27019:27017 --env MONGO_INITDB_ROOT_USERNAME=ashry --env MONGO_INITDB_ROOT_PASSWORD=ashry mongo:latest
```

## 1.14. Backup Volume

Unfortunately, there is no way that we can do for backup our Docker Volume directly. `But, we can take advantageous of the container ability to do the backup as .zip or .tar.gz`.

Steps:
1. Stop the container that connected to the volume we want to backup.
2. create a new container with 2 mount: `volume` and `bind`
```console
docker container create --name nginxbackup --mount "type=bind,source=/home/user/mongodata,destination=/backup" --mount "type=volume,source=mongodata,destination=/data" nginx:1.25-alpine
```
3. Go into the container by executing the container:
```console
docker container exec -i -t nginxbackup /bin/sh
```
4. Check the environment and compress the folder of our data:
```console
ls -a
tar cvf /backup/data.tar.gz /data/
```
5. Check your local host by checking into the backup folder and try to extract it
```console
tar xf /home/user/mongodata/data.tar.gz
```
6. Stop the container
```console
docker container stop nginxbackup
```
7. Remove the container
```console
docker container rm nginxbackup
```

* Alternative, we can do the backup using this command `docker container run` dan `--rm`, example:

```console
docker container run --rm --name nginxbackup --mount "type=bind,source=/home/user/mongodata,destination=/backup" --mount "type=volume,source=mongodata,destination=/data" nginx:1.25-alpine tar cvf /backup/data.tar.gz /data/
```

## 1.15. Restore Value

Sometimes we just want to check our volume if it corrupted or not before restoring, then we can do that by these steps:

Steps:
1. Create new volume for restore location data backup
2. Create a new container with 2 mounts using `bind` and `volume` from system host which has the backup files
3. Restore the data by extracting the data from fhe system host to the new volume
4. delete the container that we used for restoring the data
5. The new volume containing the restored data




