# Docker Compose Tutorial

## Table of Contents


## 1. YAML file

Why `yaml` file? because we will write our docker compose in `yaml` file. Yaml file works similar to `JSON`, instead of using any braces, it's use indetation only.

Example:

```yaml
# .yaml file

firstName: Ashry
lastName: Ramadhan
nasionality: Indonesia
```

```json
// .json file

{
    firstName: "Ashry",
    lastName: "Ramadhan",
    nationality: "Indonesia"
}
```

* Yaml Array

```yaml
hobbies:
    - Programming
    - Reading
    - Gaming
```

* Yaml Nested Object:

```yaml
address:
    street: Orchard rd.
    city: Woodland
    country: Singapore
```

* Yaml Array Nested Object

```yaml
wallet:
    - type: cash
      amount: 100000
    - type: debit
      amount: 100000
```

```json
{
    "wallet": [
        {
            "type": "cash",
            "amount": 100000,
        },
        {
            "type": "debit",
            "amount": 100000,
        }
    ]
}
```

## 2. Creating Container

```yaml
version: 3.8

services:
    nginx-example: # just a key, but must be unique
        container_name: nginx-example # the container name
        image: nginx:latest # using which image
```

* To build the container, just use this command:

```console
docker compose create
```

* To run all of the containers in our `docker-compose.yaml` in parallel, just use this command:

```console
docker compose start
```

* To check all of the containers in our `docker-compose.yaml` in parallel, just use this command:

```console
docker compose ps
```

* To stop all of the containers in our `docker-compose.yaml` in parallel, just use this command:

```console
docker compose stop
```


* To delete all of the `network`, `volume`, and `container` inside of our `docker-compose.yaml`, just use this command:

```console
docker compose down
```

## 3. Project Name

By default the project name of our docker is the `folder` name.

* To see which project is running, use this command:

```console
docker compose ls
```

## 4. Service

Service is a configuration where the docker compose save it's configurations. We can add more than one service.

## 5. Port

```yaml
version: "3"

services:
    nginx-port1:
        image: nginx:latest
        container_name: nginx-port1
        ports:
            - protocol: tcp
              published: 8080
              target: 80
    nginx-port2:
        image: nginx:latest
        container_name: nginx-port2
        ports: 
            - "8081:80"
```

## 6. Environment Variable

```yaml
version: "0.0.1"

services:
    mongodb-example:
        image: mongo:latest
        container_name: mongo-example
        ports:
            - "27017:27017"
        environment:
            MONGO_INITDB_ROOT_USERNAME: ashry
            MONGO_INITDB_ROOT_PASSWORD: ashry
            MONGO_INITDB_DATABASE: admin
```

## 7. Bind Mount

The syntax will be: `SOURCE:DESTINATION:MODE`, and need to use `volume` key. `MODE`: `rw` (read write) `ro` (read only)

```yaml
version: "0.0.1"

services:
    mongodb-example:
        image: mongo:latest
        container_name: mongo-example
        ports:
            - "27017:27017"
        environment:
            MONGO_INITDB_ROOT_USERNAME: ashry
            MONGO_INITDB_ROOT_PASSWORD: ashry
            MONGO_INITDB_DATABASE: admin
        volumes:
            - type: bind
              source: "./data-mongo2"
              target: "/data/db"
              read_only: false
```

## 8. Create Volumes

We can create volume with docker-compose.

```yaml
version: "0.0.1"

volumes:
    mongo-data1:
        name: mongo-data1
    mongo-data2:
        name: mongo-data2
```

To use the volume we already created, we can use this syntax:

```yaml
volumes:
    - type: volume
      source: mongo-data2
      target: "/data/db"
      read_only: false
```

When we run the command `docker compose down` it will not delete our volumes, because it can be dangerous, we can only delete it manually using `docker volume rm <volume-name>` command.

## 9. Network

We can create `docker network` also using `docker compose`. 

:warning: **Note**:
> By default docker compose will join all of the containers into one `network`, so we don't have to create the network manually. We can check it using `inspect` command. But, if we want to create our `network` manually we can use docker compose command.

```yaml
version: "3.9"

networks:
    network_example:
        name: network_example
        driver: bridge
```

After creating the `network` we can use it by using this syntax:

```yaml
networks:
    - network_example
```

## 10. Depens On

Sometimes we want to create a docker container after we creating the previous container, in other words `Docker A depens on Docker B`. 

```yaml
mongodb-express-example:
    image: mongo-express:latest
    container_name: mongodb-express-example
    depens_on:
        - mongodb-example
    ports:
        - "8081:8081"
    environment:
        ME_CONFIG_MONGODB_ADMINUSERNAME: ashry
        ME_CONFIG_MONGODB_ADMINPASSWORD: ashry
        ME_CONFIG_MONGODB_SERVER: mongodb-example
    networks:
        - network_example
```

## 11. Restart

By default when the docker container stop running, it will not goint to restart again, we need to run it manually. But, we can do restart automatically using docker compose. 

Here are the few restart values:
* no: default
* always: always restart
* on-failure: will restart if the container error when exit
* unless-stopped: always restart contaier, unless restart manually

```yaml
mongodb-express-example:
    image: mongo-express:latest
    container_name: mongodb-express-example
    restart: always
    depens_on:
        - mongodb-example
    ports:
        - "8081:8081"
    environment:
        ME_CONFIG_MONGODB_ADMINUSERNAME: ashry
        ME_CONFIG_MONGODB_ADMINPASSWORD: ashry
        ME_CONFIG_MONGODB_SERVER: mongodb-example
    networks:
        - network_example
```

## 12. Monitoring Docker Events

We want to monitoring docker events in real-time we can use:

```console
docker events
docker events --filter "container=name"
```

## 13. Resource Limit

we use `deploy` command for setting resource limit using `docker compose`.

```yaml
version: "3"

services:
    nginx-example:
        image: nginx:latest
        container_name: nginx-example
    ports:
        - "8080:80"
    deploy:
        resources:
            reservations:
                cpus: "0.25"
                memory: 50M
            limits:
                cpus: "0.5"
                memory: 100M
```

## 14. Build from Dockerfile

We can build our dockerfile using docker compose. we need to set some attributes:

* context: path to dockerfile
* dockerfile: filename of the dockerfile
* args: build arguments for `--build-args`

By default docker will create random name for our images. If we want to determine it ourself, we can add `image` attributes in the `Docker Compose` configurations.

To build the image use this command:

```console
docker compose build
```

```console
build/
    /app/
        - Dockerfile
        - main.go
```

```yaml
app:
    container_name: app
    build:
        context: "./app"
        dockerfile: Dockerfile
    image: "app-golang:1.0.0"
    environment:
        - "APP_PORT=8080"
    ports:
        - "8080:8080"
```

:warning: Notes: 
* `docker compose down` comand will not delete the image generated from `docker compose`. Thus, we are going to do it manually.'
* If you want to rebuild all the things including the images, you need to `docker compose down` the container first, then rebuild again with `docker compose build` and `docker compose create`.

## 15. Healthcheck

```yaml
version: "3"

services:
    nginx-example:
        image: nginx:latest
        container_name: nginx-example
    ports:
        - "8080:80"
    healthcheck:
        test: [ "CMD", "curl", "-f", "http://localhost:8080/health" ]
        interval: 5s
        timeout: 5s
        retries: 3
        start_period: 5s
        disbaled: false
    deploy:
        resources:
            reservations:
                cpus: "0.25"
                memory: 50M
            limits:
                cpus: "0.5"
                memory: 100M
```

## 16. Extend Service

Sometimes we want to run our apps on different servers: local, development, and production.

`Extend Service` can merge few configurations into one. Thus, we could create common and special configuration.

:exclamation: we can use `-f <file-name>.yaml` to use any other `yaml` file other than `docker-compose.yaml`.

Let say you have `dev.yaml` and `prod.yaml`, then you can just change your environment variables on `docker compose`

```yaml
# dev.yaml

version: "3.9"

services:
    app:
        environment:
            - "MODE=dev"
```

```yaml
# prod.yaml

version: "3.9"

services:
    app:
        environemtn:
            - "MODE=prod"
```

```console
docker compose -f dev.yaml create
docker compose -f prod.yaml create
```