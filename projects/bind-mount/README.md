Bind mount directly map a directory or file from the host machine into the container.

The host directory is referenced by its absolute path and the container has direct access to it.

Let's create a folder `my-content`.

In the folder `my-content`, we create a page `index.html` as below:

     $ mkdir ~/my-content
     $ echo "<h1>Hello from Bind Mount!!</h1>" > ~/my-content/index.html

Now start the nginx container with bind mount using script [**run.sh**](https://github.com/manwar/docker-guide/blob/master/projects/bind-mount/run.sh):

$ docker run -d --name my_nginx -v ~/my-content:/usr/share/nginx/html -p 8080:80 nginx

**NOTE:** The `-v` switch is also used to create docker volume.

Access the webserver using script [**exec.sh**](https://github.com/manwar/docker-guide/blob/master/projects/bind-mount/exec.sh):

     $ curl http://localhost:8080

You should see the below output in the console:

     Hello from Bind Mount!!

Let's update the index.html in the host machine.

     $ echo "<h1>Hello again from Bind Mount!!</h1>" > ~/my-content/index.html

Access the webserver now, again using script [**exec.sh**](https://github.com/manwar/docker-guide/blob/master/projects/bind-mount/exec.sh):

     $ curl http://localhost:8080

You should see the below output in the console:

     Hello again from Bind Mount!!

Finally do the cleanup using script [**rm.sh**](https://github.com/manwar/docker-guide/blob/master/projects/bind-mount/rm.sh).
