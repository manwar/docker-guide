In this project, we are using the same [**JS application**](https://github.com/manwar/docker-guide/tree/master/projects/js-app-env) from previous example.

Let update the configuration file and use docker variables: `${mongo_admin_user}` and `${mongo_admin_pass}` instead as shown below:

    version: '3.8'
    services:
      js-app:
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

Let's create env file `docker-compose.env` as below:

     mongo_admin_user=admin
     mongo_admin_pass=supersecret
     
Then start up the containers like this:

    $ docker-compose --env-file docker-compose.env up -d

List the containers:

    $ docker ps
    CONTAINER ID   IMAGE           COMMAND                  CREATED             STATUS             PORTS                                             NAMES
    8baed9b60f9f   mongo-express   "/sbin/tini -- /dock…"   23 minutes ago      Up 23 minutes      0.0.0.0:8081->8081/tcp, [::]:8081->8081/tcp       js-app_mongo-express_1
    c3b3c03f718a   mongo           "docker-entrypoint.s…"   23 minutes ago      Up 23 minutes      0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   js-app_mongodb_1
    11c1b89ba789   js-app_js-app   "docker-entrypoint.s…"   23 minutes ago      Up 23 minutes      0.0.0.0:3000->3000/tcp, [::]:3000->3000/tcp       js-app_js-app_1    
Access the Mongo Express: `http://localhost:8081` using default credentials `admin/pass`.

Access the JS application: `http://localhost:3000`

For this we would create a new database `my-db` in `Mongo Express`.

Then create a new collection `my-collection` inside the database `my-db`.

Finally we add new document in the collection `my-collection`:

    {
       "_id": ObjectId(),
       "myid": 1,
       "data": "some dynamic data loaded from DB"
    }

Refresh the `JS` application, you should see the new document listed there.

To stop all the containers do this:

    $ docker-compose --env-file docker-compose.env down
