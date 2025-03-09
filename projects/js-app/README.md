In this project, we are creating three containers: `js-app`, `mongodb` and `mongo-express`.

The `JS app` is borrowed from [**here**](https://gitlab.com/twn-youtube/docker-compose-crash-course).

We create folder `app` first in the root folder.

Inside the `app` folder, we create `index.html` as below:

    <html lang="en">
    <style>
        .container {
          margin: 40px auto;
          width: 80%;
        }

        hr {
          width: 400px;
          margin-left: 0;
        }
        h3 {
          display: inline-block;
        }
        #static {
          color: red;
        }
        #dynamic {
          color: green;
        }
    </style>
    <script>
        (async function init() {
            const cont = document.getElementById('container');
            const response = await fetch('http://localhost:3000/fetch-data', {
              method: "GET",
                headers: {
                  'Accept': 'application/json',
                  'Content-Type': 'application/json'
                }
            });
            const jsonResponse = await response.json();
            document.getElementById('dynamic').textContent = jsonResponse.data;
        })();
    </script>
    <body>
        <div class='container' id='container'>
          <h1>Welcome to this Super Sophisticated App</h1>
          <span>Some static data:  </span><h3 id="static">this is hard-coded in index.html</h3>
          <hr />
          <span>Data from MongoDB:  </span><h3 id="dynamic"></h3>
          <hr />
        </div>
    </body>
  </html>

Then we create `server.js` inside the `app` folder as below:

    let express = require('express');
    let path = require('path');
    let fs = require('fs');
    let MongoClient = require('mongodb').MongoClient;
    let bodyParser = require('body-parser');
    let app = express();

    const DB_USER = process.env.MONGO_DB_USERNAME
    const DB_PASS = process.env.MONGO_DB_PWD

    app.use(bodyParser.urlencoded({
      extended: true
    }));
    app.use(bodyParser.json());

    app.get('/', function (req, res) {
      res.sendFile(path.join(__dirname, "index.html"));
    });

    // when starting app locally, use "mongodb://admin:password@localhost:27017" URL instead
    let mongoUrlDockerCompose = `mongodb://${DB_USER}:${DB_PASS}@mongodb`;

    // pass these options to mongo client connect request to avoid DeprecationWarning 
    // for current Server Discovery and Monitoring engine
    let mongoClientOptions = { useNewUrlParser: true, useUnifiedTopology: true };

    // the following db and collection will be created on first connect
    let databaseName = "my-db";
    let collectionName = "my-collection";

    app.get('/fetch-data', function (req, res) {
      let response = {};
      MongoClient.connect(mongoUrlDockerCompose, mongoClientOptions, function (err, client) {
        if (err) throw err;

        let db = client.db(databaseName);

        let myquery = { myid: 1 };

        db.collection(collectionName).findOne(myquery, function (err, result) {
          if (err) throw err;
          response = result;
          client.close();

          // Send response
          res.send(response ? response : {});
        });
      });
    });

    app.listen(3000, function () {
      console.log("app listening on port 3000!");
    });

Finally `package.json` in the same folder `app`.

    {
      "name": "developing-with-docker",
      "version": "1.0.0",
      "description": "",
      "main": "server.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "start": "node server.js"
      },
      "author": "Nana Janashia",
      "license": "ISC",
      "dependencies": {
        "body-parser": "^1.20.2",
        "express": "^4.18.2",
        "mongodb": "^4.16.0"
      }
    }

Now we have `JS` application defined.

Let's create `Dockerfile` now:

    FROM node:20-alpine
    RUN mkdir -p /home/app
    COPY ./app /home/app
    WORKDIR /home/app
    RUN npm install
    CMD ["node", "server.js"]

Finally, we need configuration file for `docker-compose.yml`.

    version: '3.8'
    services:
      js-app:
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

Start the container:

    $ docker-compose up -d

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
