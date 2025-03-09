# Docker Guide


### What is Docker?

  - Virtualization Software
  - Makes developing and deploying applications easier
  - Packages application with all dependencies
  - Easily shared and distributed

### Development process using containers

  - Own isolated environment
  - Start service as a Docker container using command e.g. `docker run postgres`
  - The command is same for all operating systems
  - Same command for all services e.g. `docker run redis`

Docker standardizes process of running any service on any local dev environment.

It is easier to run different versions of same application without any conflicts.

### Deployment process using containers

  - First we need to have Docker runtime on the server, one time only
  - Then run Docker command to fetch and run the Docker artifacts

### Virtual Machine vs Docker

To understand this, let's explore how an OS is made up. There are 2 main layers:

  - OS Kernel
  - OS Application Layer

OS Kernel layer is responsible to interact with the hardware.

Kernel is at the core of every OS. It interacts between hardware and software components.

    OS Application Layer <=> OS Kernel <=> Hardware

VM is also a virtualization tool but they differ in what part of OS do they virtualize.

Docker virtualize the OS Application Layer.

A docker contains OS application layer. Also services and apps installed on top of that layer. 

It uses the kernel of the host as it doesn't have it's own kernel.

A virtual machine contains OS application layer and OS kernel. 

It doesn't use host kernel, instead it uses it's own kernel.

    VM1: App A, Guest OS 1
    VM2: App B, Guest OS 2

Docker images are much smaller as it doesn't have its own kernel

Docker containers take seconds to start as VM take minutes

VM is compatible with all `OS` i.e. on `Windows` host operating systems, we can have guest operating system as `Linux` and `MacOS`.

`Docker` is only compatible with `Linux` distros.

##### Virtual Machine

    - Hardware
    - Host OS e.g. Ubuntu    
    - Hypervisor e.g VMWare
      - Ubuntu (VM)
      - CentOS (VM)
      - Debian (VM)

##### Docker

    - Hardware
    - Host OS e.g. Ubuntu
    - Docker Engine
      - Ubuntu (Docker Image)
      - CentOS (Docker Image)
      - Debian (Docker Image)

The docker container is like a micro computer.

### Docker Desktop

It allows to run `Linux` containers on `Windows` or `MacOS`.

It uses a `Hypervisor` layer with a lightweight `Linux` distro.

### Install Docker

You can download the docker from the official website and follow the instructions for your OS.

For me, I am running `Ubuntu 24.04.01 LTS` in `WSL2`.

I found this [**installation guide**](https://gist.github.com/dehsilvadeveloper/c3bdf0f4cdcc5c177e2fe9be671820c7) very handy.

It worked like a charm for me.

#### Step 1: Update the system

    $ sudo apt update && sudo apt upgrade -y
	
#### Step 2: Install dependencies

    $ sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
	
#### Step 3: Add Docker GPG Key

    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
	
#### Step 4: Add the Docker Repository

    $ echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
	
#### Step 5: Install Docker Engine	

    $ sudo apt update
    $ sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
	
#### Step 6: Add yourself to Docker Group

Add your user to the Docker group to avoid the need to use sudo on every Docker command:

    $ sudo usermod -aG docker $USER
	
#### Step 7: Restart WSL via the Windows command line (Powershell).

    > wsl --shutdown	
	
#### Step 8: Access Ubuntu again. Check if Docker was installed correctly on the Ubuntu terminal:

    $ docker --version
    Docker version 28.0.1, build 068a01e

### Docker Images vs Docker Containers

Docker image is an executable application artifact. It includes app source code but also complete environment configuration. 

We can add environment variables, create directories, files etc.

Once docker image is downloaded and run. It starts the application. 

The running instance of an image is called Docker container.

From one docker image, we can run multiple containers.

To list docker images:

    $ docker images
    REPOSITORY   TAG       IMAGE ID   CREATED   SIZE

To list docker containers:

    $ docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

 ### Docker Registry

 As we now know that we run containers from images.

 How do we get these images?

`Docker Registry` is a storage and distribution system for `Docker` images.

`Docker` hosts one of the biggest `Docker Registry` called [**Docker Hub**](https://hub.docker.com).

You don't need to sign up to browse and lookup docker images on `Docker Hub`.

Every docker images comes with tag i.e. version.

To download a docker image, use the command `docker pull {name}:{tag}`

    $ docker pull nginx:1.27

Now if we list docker images, we should have one.

    $ docker images
    REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
    nginx        1.27      b52e0b094bc0   4 weeks ago   192MB

If we don't mention the tag when pulling docker image then it would download the latest.

    $ docker pull nginx
    $ docker images
    REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
    nginx        1.27      b52e0b094bc0   4 weeks ago   192MB
    nginx        latest    b52e0b094bc0   4 weeks ago   192MB

How how do we run the image and create a container?

    $ docker run nginx:1.27

We have a container running now.

Open up a new terminal and type the command below:

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES
    518dddf21a2d   nginx:1.27   "/docker-entrypoint.…"   13 seconds ago   Up 12 seconds   80/tcp    kind_grothendieck

In the other terminal where we ran the command `docker run nginx:127`, container logs still blocking the control.

Let's cancel the run `CTRL + c`.

Now run the below command, no container is running now:

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES

To avoid the docker log blocking the terminal, we can run the container and detach as well.

    $ docker run -d nginx:1.27
    3f35dc78986b4c1fe172005b3cc650e978804131d71706ce30609ff02e7457cf
    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES
    3f35dc78986b   nginx:1.27   "/docker-entrypoint.…"   13 seconds ago   Up 12 seconds   80/tcp    kind_grothendieck

If you want to inspect the docker log then you use command `docker logs <container_id>`.

    $ docker logs 3f35dc78986b	

We can run the container without manually pulling the docker image.

Below we want to run `nginx 1.23`, which we don't have it locally.

It will look up locally first and when it can't find then it would go and pull it for you.

    $ docker run -d nginx:1.23

Now if we check the containers, we see two containers running.

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES
    beb858459a56   nginx:1.23   "/docker-entrypoint.…"   3 seconds ago    Up 2 seconds    80/tcp    stoic_lumiere
    3f35dc78986b   nginx:1.27   "/docker-entrypoint.…"   55 minutes ago   Up 55 minutes   80/tcp    vigorous_elbakyan

### Container Port vs Host Port

Application inside the container runs in an isolated Docker network.
 
This allows us to run the same app running on the same port multiple times.

We need to expose the container port to the host.

#### Port Binding

     Bind the container's port to the host's port to make the service
     available to the outside world.

Every application runs on specific port e.g. nginx runs on port 80, redis runs on port 6379.

You can get this information when you type `docker ps` command.

If we try to reach port 80 outside the container as in the browser `http://localhost:80` then you would get error `The site can't be reached`

Let's stop the container first:

    $ docker stop 3f35dc78986b

Now we would run the container with port binding like below:

    $ docker run -d -p 9000:80 nginx:1.27

Check the container status

    $ docker run -d -p 9000:80 nginx:1.27
    bf4e2dc18a602ecc067a72e1ea076068deb26e0d4000c506a41d6aeb0047dba1

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                     NAMES
    bf4e2dc18a60   nginx:1.27   "/docker-entrypoint.…"   2 seconds ago   Up 2 seconds   0.0.0.0:9000->80/tcp, [::]:9000->80/tcp   reverent_ellis


Check in the browser if you can access the nginx: `http://localhost:9000`

You can check the container's log:

    $ docker logs bf4e2dc18a60

Generally it is advisable to port bind the same port. 

    $ docker stop bf4e2dc18a60

    $ docker run -d -p 80:80 nginx:1.27
    bf4e2dc18a602ecc067a72e1ea076068deb26e0d4000c506a41d6aeb0047dba1

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
    bf4e2dc18a60   nginx:1.27   "/docker-entrypoint.…"   2 seconds ago   Up 2 seconds   0.0.0.0:80->80/tcp, [::]:9000->80/tcp   reverent_ellis

**NOTE**

Docker run command creates a new container everytime and doesn't re-use the previous container.

We have executed `docker run`  multiple times but when we do `docker ps` it only shows one. 

However those containers still exist.

     $ docker ps -a

To remove the container:

     $ docker rm bf4e2dc18a60

#### To stop all containers: `docker stop $(docker ps -q)`
#### To remove all containers: `docker rm $(docker ps -a -q)`

You can even start the container that we stopped earlier, rather than creating a new.

     $ docker start bf4e2dc18a60

You can give a name to a container:

    $ docker run --name nginx-1 -d -p 9000:80 nginx:1.27

    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                     NAMES
    5058136e20a2   nginx:1.27   "/docker-entrypoint.…"   4 seconds ago   Up 4 seconds   0.0.0.0:9000->80/tcp, [::]:9000->80/tcp   nginx-1

### Registry vs Repository

`Docker Registry` is a service providing storage and can be hosted by a third party like `AWS` or by yourself. 

It is also a collection of repositories.

`Docker Repository` is a collection of related images with the same name but different versions.

`Docker Hub` is a registry.

On `Docker Hub`, you can have private or public repositories for your application.

### Create docker image

`Dockerfile` can be used to create docker image.

Let's build a simple `Hello World` program in `Python`.

We would create `app.py`

    print("Hello, World!")

Now we create another file `Dockerfile` as below:

    # Use an official Python runtime as a parent image
    FROM python:3.14-rc-bookworm

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY app.py /app

    # Run app.py when the container launches
    CMD ["python", "app.py"]

Build the image:

    $ docker build -t my-python-app .

Run the container:

    $ docker run my-python-app

We can create an image as above but this time using `docker-compose`.

For this we would need to install `docker-compose`:

    $ sudo apt install docker-compose

Create a file `docker-compose.yml`

    version: '3.8'

    services:
      my-python-app:
        build: .
        container_name: my-python-app-container

The `version: '3.8'`, specifies the version of the Docker Compose file format.

The `services:`, defines the services that will be run.

`my-python-app`, the name of the service.

`build: .`, tells Docker Compose to build the Docker image using the Dockerfile in the current directory.

`container_name: my-python-app-container`, specifies the name of the container.

Now build and run the container:

    $ docker-compose up

Stop the container:

    $ docker-compose down

### Docker Compose

It's a tool for defining and running multi-container Docker applications.

Without docker-compose, you start 2 docker containers

1) Create a docker network

       $ docker network create mongo-network
       7ca8d14845c684336d8e494515670675a8685646455e3761a0e88b0ff4e3234e
   
       $ docker network ls
       NETWORK ID     NAME            DRIVER    SCOPE
       1240c8ea9eb8   bridge          bridge    local
       d0b60d358dfb   host            host      local
       7ca8d14845c6   mongo-network   bridge    local
       7700c2bc9f69   none            null      local

3) Start MongoDB container

       $ docker run -d \
         -p 27017:27017 \
         -e MONGO_INITDB_ROOT_USERNAME=admin \
         -e MONGO_INITDB_ROOT_PASSWORD=supersecret \
         --network mongo-network \
         --name mongodb \
         mongo

       $ docker ps

4) Start Mongo Express Container

       $ docker run -d \
         -p 8081:8081 \
         -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
         -e ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret \
         -e ME_CONFIG_MONGODB_SERVER=mongodb \
         --network mongo-network \
         --name mongo-express \
         mongo-express

       $ docker ps

Open browser and visit `http://localhost:8081`

Get the credentials `admin:pass` from docker logs

    $ docker logs mongo-express

As we see, we have 2 containers i.e. Mongo Express dependent on MongoDB.

In the above example, we run 2 containers on the same docker network. 

It works just fine, if you have 2-3 containers.

With `docker-compose`, we create a single `YAML` configuration file for all application services.

Also you create and start all the services with a single command.

Let's convert docker commands to docker compose configuration.

Here is the `docker-compose.yml` file:

    version: '3.8'
    services:
      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=admin
          - MONGO_INITDB_ROOT_PASSWORD=supersecret

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
          - ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret
          - ME_CONFIG_MONGODB_SERVER=mongodb

Missing network name in the configuration?

Well, `docker-compose` sets up a single network for the application by default.

It also gives container name in format: `<folder_name>-<image_name>-1` by default.

So if the above `docker-compose.yml` is in the folder `project` then it would create two containers as below:

    project-mongodb-1
    project-mongo-express-1

Now stop all containers first and then remove.

    $ docker stop mongodb mongo-express

    $ docker rm mongodb mongo-express

    $ docker network rm mongo-network

Check network afterwards:

    $ docker network ls

Check no container running:

    $ docker ps

Now start the containers:

    $ docker-compose -f docker-compose.yml up

**NOTE:** The `-f` flag is optional if the configuration file is named as `docker-compose.yml` or `docker-compose.yaml` and in the current folder.

If you want to see the default network created by `docker-compose` then do this:

    $ docker network ls

Check the containers, you should see 2 in the list.

    $ docker ps

You will notice one thing, that logs of both containers are mixed up.

You can also configure the dependency in the docker compose file like below:

    version: '3.8'
    services:
      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=admin
          - MONGO_INITDB_ROOT_PASSWORD=supersecret

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
          - ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

Open the `Mongo Express` application in the browser: `http://localhost:8081`. 

Then create a database `my-db` and inside the database, create a collection `my-collection`.

You should check the log showing all activities.

If you want to run docker compose in `detach` mode, do this:

    $ docker-compose -f docker-compose.yml up -d

To shutdown all services, do this:

    $ docker-compose -f docker-compose.yml down

If you noticed, it not only stopped the containers but also removed it too.

It also removed the network as well.

    $ docker ps -a

**NOTE:** If you remove the container, you loose all the changes done in the container. By default, no persistence happens.

However if you stop the container and start again, you still have all the changes.

So you would do something like below:

    $ docker-compose -f docker-compose.yml start -d

    $ docker-compose -f docker-compose.yml stop

Let's add a document in the collection `my-collection` we created above in the database `my-db`.

    {
       "_id": ObjectId(),
       "myid": 1,
       "data": "some dynamic data loaded from DB"
    }
    
We would try to fetch this document in the `JS` application.

Let's add a simple JS application to interact with the MongoDB.

[**Source**](https://gitlab.com/twn-youtube/docker-compose-crash-course) for the JS application.

Get the clone of the above repository locally.

Copy the `docker-compose.yaml` file we created above inside the root folder of the repository.

Now we will edit the docker compose file to add the JS application:

We are building image as we don't have image for JS application using the `Dockerfile` in the root folder.

    version: '3.8'
    services:
      my-app:
        build: .
        ports:
          - 3000:3000
        environment:
          - MONGO_DB_USERNAME=admin
          - MONGO_DB_PWD=supersecret

      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=admin
          - MONGO_INITDB_ROOT_PASSWORD=supersecret

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
          - ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

Since we moved the `docker-compose.yml` file to the clone repository, the `docker-compose` would now create container name differently.

First stop all containers:

    $ docker-compose -f docker-compose.yml down

Check all containers:

    $ docker ps -a

In order to use the existing container, we can override the project name,
  
    $ docker-compose --project-name project -f docker-compose.yml up -d

This wouldn't create new containers but re-use the same container created earlier.

You can access the `JS` application: `http://localhost:3000`

**NOTE:** It is not recommended to hardcode sensitive data in the docker compose configuration file.

To solve the problem, we can create `docker variables` as environment variables as below:

    version: '3.8'
    services:
      my-app:
        build: .
        ports:
          - 3000:3000
        environment:
          - MONGO_DB_USERNAME=${MONGO_ADMIN_USER}
          - MONGO_DB_PWD=${MONGO_ADMIN_PASS}

      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=${MONGO_ADMIN_USER}
          - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ADMIN_PASS}

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_ADMIN_USER}
          - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_ADMIN_PASS}
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

To test the changes, first stop the containers:

    $ docker-compose -f docker-compose.yml -p project stop

In the terminal, we can define the environment variables like below:

    $ export MONGO_ADMIN_USER=admin

    $ export MONGO_ADMIN_PASS=supersecret

Now start the containers again

    $ docker-compose -f docker-compose.yml -p project start

There is even a better way to use secrets in docker compose without having to use environment variables.

We would create a text file `docker-compose.env` like below:

     mongo_admin_user=admin
     mongo_admin_pass=supersecret

**NOTE:** Please make sure you add this file in `.gitignore` so it doesn't get public.

Now we can reference this in the configuration file like below:

    version: '3.8'
    services:
      my-app:
        build: .
        ports:
          - 3000:3000
        environment:
          - MONGO_DB_USERNAME=${mongo_admin_user}
          - MONGO_DB_PWD=${mongo_admin_pass}

      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=${mongo_admin_user}
          - MONGO_INITDB_ROOT_PASSWORD=${mongo_admin_pass}

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=${mongo_admin_user}
          - ME_CONFIG_MONGODB_ADMINPASSWORD=${mongo_admin_pass}
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

You can reference the `docker-compose.env` file like this:

    $ docker-compose --env-file docker-compose.env -f docker-compose.yml -d up

There is another alternative to hide sensitive data i.e. `Docker Secrets`.

The docker secrets works in `swarm mode`.

    $ docker swarm init

Check the status, you should `Swarm: active`

    $ docker info    

There are 2 ways to create secrets: `using file` and `using CLI`

Let's create file `password.txt`:

    $ cat password.txt
    password123

With docker secrets, we have the following options:`create`, `inspect`, `ls` and `rm`.

    $ docker secret <option>

Let's create secret key `db_pass` using the file `password.txt`:

    $ docker secret create db_pass password.txt

List all the secrets:

    $ docker secret ls

Inspect a secret key:

    $ docker secret inspect db_pass

Now we would create secret key using `CLI`:

    $ echo "admin" | docker secret create db_user -

We will now use the secret when creating service:

The default location of secrets inside the container is `/run/secrets/`.

    $ docker service create \
      --name postgres-1 \
      --secret db_user \
      --secret db_pass \
      -e POSTGRES_USER_FILE=/run/secrets/db_user \
      -e POSTGRES_PASSWORD_FILE=/run/secrets/db_pass \
      postgres

List services:

    $ docker service ls

Get the container id

    $ docker ps
    $ docker exec -it <container_id> bash
    $ cd /run/secrets
    $ ls
    $ cat db_user
    $ exit

To remove the secret, first we need to stop the service.

    $ docker service rm postgres1
    
Check the service, there shouldn't be any:

    $ docker ps
    $ docker service ls

Now remove the secret:

    $ docker secret rm <secret_id>

In the configuration file, we can create top-level `secrets` element and reference the secret in the `secrets` attribute in the `services` like below:

    version: '3.8'
    secrets:
      my_secret:
        file: ./my_secret.txt
    services:
      my-app:
        build: .
        ports:
          - 3000:3000
        secrets:
          - my_secret

      mongodb:
        image: mongo
        ports:
          - 27017:27017
        secrets:
          - my_secret

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        secrets:
          - my_secret
	environment:
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

We can create docker image and push to docker registry.

For this, I have created a private registry on the DockerHub: `manwardock/my-app`.

Let's build image first:

    $ docker build -t manwardock/my-app:1.0 .

List the image:

    $ docker images

Login to docker:

    $ docker login

Now push the image:

    $ docker push manwardock/my-app:1.0

Go to `Docker Hub` web portal, you should have your image listed there.

Once we have pushed the image, we can use it in the configuration file as below:

    version: '3.8'
    services:
      my-app:
        build: manwardoc/my-app:1.0
        ports:
          - 3000:3000
        environment:
          - MONGO_DB_USERNAME=${MONGO_ADMIN_USER}
          - MONGO_DB_PWD=${MONGO_ADMIN_PASS}

      mongodb:
        image: mongo
        ports:
          - 27017:27017
        environment:
          - MONGO_INITDB_ROOT_USERNAME=${MONGO_ADMIN_USER}
          - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ADMIN_PASS}

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_ADMIN_USER}
          - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_ADMIN_PASS}
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

### Commands

###### IMAGES:

List all Local images

    $ docker images

Delete an image

    $ docker rmi <image_name>

Remove unused images

    $ docker image prune

Build an image from a Dockerfile

    $ docker build -t <image_name>:<version> . //version is optional
    $ docker build -t <image_name>:<version> . -no-cache //build without cache

###### CONTAINER:

List all Local containers (running & stopped)

    $ docker ps -a

List all running containers

    $ docker ps

Create & run a new container

If image not available locally, it’ll be downloaded from DockerHub

    $ docker run <image_name>

Run container in background

    $ docker run -d <image_name>

Run container with custom name

    $ docker run --name <container_name> <image_name>

Port Binding in container

    $ docker run -p<host_port>:<container_port> <image_name>

Set environment variables in a container

    $ docker run -e <var_name>=<var_value> <container_name> (or <container_id)

Start or Stop an existing container

    $ docker start|stop <container_name> (or <container_id)

Inspect a running container

    $ docker inspect <container_name> (or <container_id)

Delete a container

    $ docker rm <container_name> (or <container_id)

###### TROUBLESHOOT:

Fetch logs of a container

    $ docker logs <container_name> (or <container_id)

Open shell inside running container

    $ docker exec -it <container_name> /bin/bash
    $ docker exec -it <container_name> sh

###### Docker Hub:

Pull an image from DockerHub

    $ docker pull <image_name>

Publish an image to DockerHub

    $ docker push <username>/<image_name>

Login into DockerHub

    $ docker login -u <image_name>

Or

    $ docker login

//also, docker logout to remove credentials

Search for an image on DockerHub

    $ docker search <image_name>

###### VOLUMES:

List all Volumes

    $ docker volume ls

Create new Named volume

    $ docker volume create <volume_name>

Delete a Named volume

    $ docker volume rm <volume_name>

Mount Named volume with running container

    $ docker run --volume <volume_name>:<mount_path>

//or using --mount

    $ docker run --mount type=volume,src=<volume_name>,dest=<mount_path>

Mount Anonymous volume with running container

    $ docker run --volume <mount_path>

To create a Bind Mount

    $ docker run --volume <host_path>:<container_path>

//or using --mount

    $ docker run --mount type=bind,src=<host_path>,dest=<container_path>

Remove unused local volumes

    $ docker volume prune //for anonymous volumes

###### NETWORK:

List all networks

    $ docker network ls

Create a network

    $ docker network create <network_name>

Remove a network

    $ docker network rm <network_name>

Remove all unused networks

    $ docker network prune

###### Docker History

    $ docker history test 
    $ docker history test --no-trunk

### RESOURCES
***

[**What is Docker?**](https://www.youtube.com/watch?v=H8Lyj2D_cWo)

[**Docker Tutorial for beginners - Complete One Shot**](https://www.youtube.com/watch?v=exmSJpJvIPs)

[**Docker Containers 101**](https://www.youtube.com/watch?v=eGz9DS-aIeY)

[**Ultimate Docker Compose Tutorial**](https://www.youtube.com/watch?v=SXwC9fSwct8)
