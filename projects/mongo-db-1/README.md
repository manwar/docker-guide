This application has two containers: `mongodb` and `mongo-express`.

For this we first create a docker network using script: `setup-network.sh`

    $ docker network create mongo-network

Then we create container for `mongodb` using script: `run-mongodb.sh`

    $ docker run -d \
      -p 27017:27017 \
      -e MONGO_INITDB_ROOT_USERNAME=admin \
      -e MONGO_INITDB_ROOT_PASSWORD=supersecret \
      --network mongo-network \
      --name mongodb \
      mongo

Finally we create another container for `mongo-express` using script: `run-mongoexpress.sh`

    $ docker run -d \
      -p 8081:8081 \
      -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
      -e ME_CONFIG_MONGODB_ADMINPASSWORD=supersecret \
      -e ME_CONFIG_MONGODB_SERVER=mongodb \
      --network mongo-network \
      --name mongo-express \
      mongo-express

There is a handy script `create.sh` that does the above in sequence:

    $ ./setup-network.sh
    $ ./run-mongodb.sh
    $ ./run-mongoexpress.sh

**NOTE:** You only need to run the script `create.sh` once.

Check the status of containers:

    $ docker ps
    CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS         PORTS                                             NAMES
    a8640ee4ca9c   mongo-express   "/sbin/tini -- /dock…"   27 minutes ago   Up 3 seconds   0.0.0.0:8081->8081/tcp, [::]:8081->8081/tcp       mongo-express
    4c4d171b251e   mongo           "docker-entrypoint.s…"   29 minutes ago   Up 3 seconds   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   mongodb

Access the `Mongo Express` using `http://localhost:8081`

To login, use the default credentials `admin` and `pass`.

When done, you can stop both the containers, using the script: `down.sh`

    $ docker stop mongo-express

    $ docker stop mongodb

Next time when you want to start again, just use the script: `up.sh`

    $ docker start mongodb

    $ docker start mongo-express

If you want to remove the containers and docker network completely then you do the following:

    $ docker rm mongodb mongo-express

    $ docker network rm mongo-network

Remember, once you remove the containers you loose all the changes. However if you just stop then it keeps everything.
