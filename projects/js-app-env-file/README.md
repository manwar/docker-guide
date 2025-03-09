In this project, we are using the same [**JS application**](https://github.com/manwar/docker-guide/tree/master/projects/js-app/app) from previous example.

We also copied the [**Dockerfile**](https://github.com/manwar/docker-guide/blob/master/projects/js-app/Dockerfile) from previous example.

In fact, we copied the [**docker-compose.yml**](https://github.com/manwar/docker-guide/blob/master/projects/js-app/docker-compose.yml) as well but removed the hardcoded user name and password.

Here we are using docker variables: `${MONGO_ADMIN_USER}` and `${MONGO_ADMIN_PASS}` instead as shown below:

    version: '3.8'
    services:
      js-app:
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
          - MONGO_INITDB_ROOT_PASSWORD=${MONGO_ADMIN_USER}

      mongo-express:
        image: mongo-express
        ports:
          - 8081:8081
        environment:
          - ME_CONFIG_MONGODB_ADMINUSERNAME=${MONGO_ADMIN_USER}
          - ME_CONFIG_MONGODB_ADMINPASSWORD=${MONGO_ADMIN_USER}
          - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
          - "mongodb"

We can define the environment variables in two ways:

    $ export MONGO_ADMIN_USER=admin
    $ export MONGO_ADMIN_PASS=supersecret

Then start up the containers like this:

    $ docker-compose up -d

or just do this in one line:

    $ MONGO_ADMIN_USER=admin MONGO_ADMIN_PASS=supersecret docker-compose up -d

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
