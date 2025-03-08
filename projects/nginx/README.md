We are going to create nginx container.

We would first pull the latest nginx from the docker registry `DockerHub`.

      $ docker pull nginx

By default it would pull the latest version of nginx.

List the image we just pulled.

      $ docker image ls
      REPOSITORY           TAG       IMAGE ID       CREATED         SIZE
      nginx                latest    b52e0b094bc0   4 weeks ago     192MB

However if we want a specific version then we can do something like below:

      $ docker pull nginx:1.27

Time to create container for nginx:

      $ docker run --name nginx -d -p 9000:80 nginx
      20f03ef44a97255e9db3fa86e912544f93598437e87ca04e33c23ce6aa93c085

The flag `--name` is used to name the container.

The flag `-d` to create the container in detached mode.

The flag `-p <host port>:<container port>` is port binding.

List the container:

      $ docker ps
      CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
      20f03ef44a97   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:9000->80/tcp, [::]:9000->80/tcp   nginx

Now you can access the nginx: `http://localhost:9000`
