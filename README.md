# Voting App with Docker and Kubernetes 

# Solution Structure:
---------------------
- Voting App: A simple web application that allows users to vote between two options. This app is built using Python and Flask. We will ctrate a docker image for this app.

- Redis: A key-value store used to store the votes in the voting app. This app is built using the official Redis Docker image. The alias for this container must be `redis` because the voting app is configured to connect to a Redis server with the hostname `redis`. There is no username or password required to connect to the Redis server.

- Result App: A simple web application that displays the results of the voting app. This app is built using JavaScript and Express. We will create a docker image for this app.


- PostgreSQL: A relational database used to store the votes from the voting app. This app is built using the official PostgreSQL Docker image. The alias for this container must be `db` because the results app is configured to connect to a PostgreSQL database with the hostname `db`. The PostgreSQL database is configured with the username `postgres` and the password `postgres`.


```javascript
var pool = new Pool({
  connectionString: 'postgres://postgres:postgres@db/postgres'
});
```

- Worker App: A background worker that processes the votes stored in the Redis database and stores them in the PostgreSQL database. This app is built using Python and RQ (Redis Queue). We will create a docker image for this app.

## Hint:
1. If you encounter connectivity issues between the containers while working, make sure that the containers are running and you are using the correct names. You can always delete the images and containers and start fresh if you run into issues.

2. Don't feel furstrated if you encounter issues while working on this project. It's normal to face challenges while working with Docker and Kubernetes. The key is to learn from the issues and keep moving forward.


```bash

STEP 1: Run the voting app with Docker
---------------------------------------
The docker image for the voting app is ready to be built and run. The docker image is built using the Dockerfile in the root directory of the project. The Dockerfile contains the instructions to build the docker image.

To build the docker image, run the following command in the directory of the project where the Dockerfile is present (`vote-app-frontend` in our case).

```bash
docker build -t voting-app-img .
```

The `-t` flag is used to tag the image with the name `voting-app-frontend`. This command will build the docker image with the tag `voting-app-frontend`.

The `.` at the end of the command specifies the build context, which is the current directory. The Dockerfile is present in the current directory, and the build context is set to the current directory.

STEP 1.2 [Optional]: Upload the docker image to Docker Hub
-----------------------------------------------
You don't have to upload the image to Dockerhub if you are working locally. But it is nice to see how this works. You can upload the docker image to Docker Hub, you need to tag the image with your Docker Hub username and the repository name. You can do this using the following command:

```bash
docker tag voting-app-frontend mhassanist/voting-app-frontend:v1
```

This command tags the docker image with the name `user/voting-app-frontend:v1`, where `user` is your Docker Hub username and `voting-app-frontend` is the repository name.

Login to Docker Hub using the following command:

```bash
docker login
```

Next, you can push the docker image to Docker Hub using the following command:

```bash
docker push mhassanist/voting-app-frontend:v1
```

STEP 2: Run the voting app docker image
----------------------------------------
Once the docker image is built, you can run the docker image using the following command:

```bash
docker run -d -p 8080:80 --name voting-app voting-app-img
```

Now you can access the voting app by opening a web browser and navigating to `http://localhost:8080`.

If you click on the `Cat` or `Dog` button, the server will crash because it cannot connect to the Redis server to store the votes. We will fix this in the next step.


STEP 3: Run the redis image to store the votes
----------------------------------------------
The voting app uses Redis as a backend to store the votes. You can run the Redis docker image using the following command:

```bash
docker run -d -p 6379:6379 --name redis redis
```


This command runs the Redis docker image in detached mode (`-d` flag) and maps the container port `6379` to the host port `6379` (`-p 6379:6379` flag). This allows the voting app to connect to the Redis server running in the container. 

Note that we don't have the Redis image in the project, so Docker will pull the Redis image from Docker Hub if it is not already present on your system.

If you test the previous step again, you will notice that the voting app is still crashing when you try to vote. This is because the voting app container **is not able to connect** to the Redis server running in the Redis container.

Steo 3.1: Rerun the voting app with a link to the Redis container
---------------------------------------------------------------------------

To fix this, you need to run the voting app container with a link to the Redis container. You can do this using the following command:

Before doing so, let's remove the existing voting app container:

```bash
docker rm voting-app
```

```bash
docker run -d -p 8080:80 --link redis:redis --name voting-app voting-app-img
```

The voting should be working now. You can access the voting app by opening a web browser and navigating to `http://localhost:8080`. You can vote for `Cat` or `Dog`, and the votes will be stored in the Redis database.

STEP 4: Run the postgres image to store the votes
------------------------------------------------
The project uses a PostgreSQL database to store the votes. You can run the PostgreSQL docker image using the following command:

```bash
docker run -d --name db -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres postgres
```

This command runs the PostgreSQL docker image in detached mode (`-d` flag) and maps the container port `5432` to the host port `5432` (`-p 5432:5432` flag). It also sets the environment variables `POSTGRES_USER` and `POSTGRES_PASSWORD` to `postgres`, which are the default username and password for the PostgreSQL database.

STEP 5: Run the result app with Docker
---------------------------------------
The result app is a simple web application that displays the results of the voting app. The result app is built using the Dockerfile in the `result-app` directory.

To build the docker image for the result app and give it access (link it) to the PostgreSQL database, run the following command in the `result-app` directory:

Build the docker image for the result app:
```bash
docker build -t results-frontend-img .
```

```bash
docker run -d --link db:db -p 8090:80 -e DB_HOST=postgres -e DB_USER=postgres -e DB_PASSWORD=postgres --name results-frontend results-frontend-img

```

STEP 6: Run the worker app with Docker
---------------------------------------
The worker app is a background worker that processes the votes stored in the Redis database and stores them in the PostgreSQL database. The worker app is built using the Dockerfile in the `worker-app` directory.

To build the docker image for the worker app and give it access (link it) to the Redis and PostgreSQL databases, run the following command in the `worker-app` directory:

Open the worker directory in your terminal and run the following command:

```bash
docker build -t worker-img .
```

```bash
docker run -d --name worker --link redis:redis --link db:db -e DB_HOST=postgres -e REDIS_HOST=redis worker-img
```


Now everything is up and running using Docker. You can access the voting app by opening a web browser and navigating to `http://localhost:8080`. You can access the results app by navigating to `http://localhost:8090`.


# Making our life easier with docker-compose
--------------------------------------------
Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services. Then, with a single command, you create and start all the services from your configuration.

Check the `docker-compose.yml` file in the root directory of the project. This file defines the services for the voting app, Redis, PostgreSQL, result app, and worker app.

To run the entire application using Docker Compose, run the following command in the root directory of the project:

```bash
docker-compose up -d
```

To stop the application, run the following command:

```bash
docker-compose down
```

You can access the voting app by opening a web browser and navigating to `http://localhost:8080`. You can access the results app by navigating to `http://localhost:8090`.

Best of luck with your project! If you have any questions or need help, feel free to ask.