This application has one `Python` script, `hello-world.py` that prints `Hello World!!` to the console.

    $ cat hello-world.py
    print("Hello World!!")

We then create `Dockerfile` to build a new docker image of our python application.

    $ cat Dockerfile
    FROM python:3.14-rc-bookworm
    WORKDIR /hello-world
    COPY hello-world.py /hello-world
    CMD ["python", "hello-world.py"]

After that we create configuration file, `docker-compose.yml` for `docker-compose`.

    $ cat docker-compose.yml
    version: '3.8'

    services:
      hello-world:
        build: .
        image: hello-world:0.1
        container_name: hello-world

Time to create the docker image:

    $ docker build -t hello-world:0.1 .

Now we can use the `docker-compose` to start the container.

    $ docker-compose up

When done, you can stop the container:

    $ docker-compose down

**NOTE:** If you want to delete the docker image then you can first find the image id `docker image ls` and then run `docker image rm <image_id>`.    
