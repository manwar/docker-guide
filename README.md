# Docker Guide


### What is Docker?

  - Virtualization Software
  - Makes developing and deploying applications easier
  - Packages application with all dependencies
  - Easily shared and distributed

### Development process using containers

  - Own isolated environment
  - Start service as a Docker container using command e.g. docker run postgres
  - The command is same for all OS
  - Same command for all services e.g. docker run redis

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

VM is compatible with all OS i.e. of Windows host OS, we can Linux guest OS and MacOS

Docker is only compatible with Linux distros

### Docker Desktop

It allows to run Linux containers on Windows or MacOS.
It uses a Hypervisor layer with a lightweight Linux distro.

### Install Docker

You can download the docker from the official website and follow the instructions of your OS.

For me, I am running Ubuntu 24.04 LTS in WSL2.

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

Docker image is an executable application artifact. It includes app source code but also complete environment configutation. 
We can add environment variables, create directories, files etc.

Once docker image is downloaded and run. It starts the application. 
The running instance of an image is called Docker container.

From one docker image, we can run multiple containers.

To list docker images:

    $ docker images
    REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
    $

To list docker containers:

    $ docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    $ 

 ### Docker Registry

 As we now know that we run containers from images.

 How do we get these images?

Docker Registry is a storage and distribution system for Docker images.

Docker hosts one of the biggest Docker Registry called [**Docker Hub**](https://hub.docker.com).

You don't need to sign up to browse and lookup docker images on docker hub.

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
    $	

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
    $

Check in the browser if you can access the nginx: `http://localhost:9000`

You can check the containers log:

    $ docker logs bf4e2dc18a60

Generally it is advisable to port bind the same port. 

    $ docker stop bf4e2dc18a60
    $ docker run -d -p 80:80 nginx:1.27
    bf4e2dc18a602ecc067a72e1ea076068deb26e0d4000c506a41d6aeb0047dba1
    $ docker ps
    CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
    bf4e2dc18a60   nginx:1.27   "/docker-entrypoint.…"   2 seconds ago   Up 2 seconds   0.0.0.0:80->80/tcp, [::]:9000->80/tcp   reverent_ellis
    $

**NOTE**

Docker run command creates a new container everytime and doesn't re-use the previous container.

We have executed `docker run`  multiple times but when we do `docker ps`  it only shows one. 

However those containers still exist.

     $ docker ps -a




