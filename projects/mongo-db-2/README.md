First we will create the configuration file, `docker-compose.yml` for `docker-compose`:

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

Let's start the container in detached mode:

    $ docker-compose up -d

List the containers:

    $ docker ps
    CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                             NAMES
    1af45924f40b   mongo           "docker-entrypoint.s…"   38 seconds ago   Up 37 seconds   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   mongo-db-2_mongodb_1
    879c6f7ed9cf   mongo-express   "/sbin/tini -- /dock…"   38 seconds ago   Up 37 seconds   0.0.0.0:8081->8081/tcp, [::]:8081->8081/tcp       mongo-db-2_mongo-express_1

We can now access the `Mongo Express` in the browser: `http://localhost:8081`

To stop and remove the container, type below:

    $ docker-compose down
    Stopping mongo-db-2_mongodb_1       ... done
    Stopping mongo-db-2_mongo-express_1 ... done
    Removing mongo-db-2_mongodb_1       ... done
    Removing mongo-db-2_mongo-express_1 ... done
    Removing network mongo-db-2_default

To stop the service only:

    $ docker-compose stop
    Stopping mongo-db-2_mongo-express_1 ... done
    Stopping mongo-db-2_mongodb_1       ... done

Again, to start the service in detached mode:

    $ docker-compose start -d
