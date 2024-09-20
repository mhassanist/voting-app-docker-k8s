# Voting App with Docker and Kubernetes 

STEP 1: Run the voting app with Docker
---------------------------------------
Docker is a containerization platform that packages your application and all its dependencies together in the form of a container image. This container image can then be run on any Docker engine.

The docker image for the voting app is ready to be built and run. The docker image is built using the Dockerfile in the root directory of the project. The Dockerfile contains the instructions to build the docker image.

To build the docker image, run the following command in the root directory of the project `where the Dockerfile is present`:

```bash
docker build -t voting-app-frontend .
```

The `-t` flag is used to tag the image with the name `voting-app-frontend`. This command will build the docker image with the name `voting-app-frontend`.

The `.` at the end of the command specifies the build context, which is the current directory. The Dockerfile is present in the current directory, and the build context is set to the current directory.

STEP 1.2 [Optional]: Upload the docker image to Docker Hub
-----------------------------------------------
You don't have to upload the image to Dockerhub if you are working locally. But it is nice to see how this works. You can upload the docker image to Docker Hub, you need to tag the image with your Docker Hub username and the repository name. You can do this using the following command:

```bash
docker tag voting-app-frontend mhassanist/voting-app-frontend:v1
```

This command tags the docker image with the name `mhassanist/voting-app-frontend:v1`, where `mhassanist` is your Docker Hub username and `voting-app-frontend` is the repository name.

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
docker run -p 8080:80 voting-app-frontend
```

Now you can access the voting app by opening a web browser and navigating to `http://localhost:8080`.



STEP 3: Run the redis image to store the votes
----------------------------------------------
The voting app uses Redis as a backend to store the votes. You can run the Redis docker image using the following command:

```bash
docker run -d -p 6379:6379 redis
```

This command runs the Redis docker image in detached mode (`-d` flag) and maps the container port `6379` to the host port `6379` (`-p 6379:6379` flag). This allows the voting app to connect to the Redis server running in the container. 

Note that we don't have the Redis image in the project, so Docker will pull the Redis image from Docker Hub if it is not already present on your system.

Steo 3.1: Rerun the voting app with a link to the Redis container
---------------------------------------------------------------------------
If you run the two containers separately and try to use the voting app, you will notice that the votes are not being stored in Redis. The app will crash when you try to vote because it cannot connect to the Redis server This is because the voting app container is not able to connect to the Redis server running in the Redis container.

To fix this, you need to run the voting app container with a link to the Redis container. You can do this using the following command:

Before doing so, let's remove the existing voting app container:

```bash
docker rm voting-app-frontend
```

```bash
docker run -d -p 8080:80 --name voting-app-frontend -e REDIS_HOST=redis --link 8a67f73192bf:redis voting-app-frontend
```


STEP 4: Run the postgres image to store the votes
------------------------------------------------
The voting app uses a PostgreSQL database to store the votes. You can run the PostgreSQL docker image using the following command:

```bash
docker run -d -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres postgres
```

This command runs the PostgreSQL docker image in detached mode (`-d` flag) and maps the container port `5432` to the host port `5432` (`-p 5432:5432` flag). It also sets the environment variables `POSTGRES_USER` and `POSTGRES_PASSWORD` to `postgres`, which are the default username and password for the PostgreSQL database.

STEP 5: Run the result app with Docker
---------------------------------------
The result app is a simple web application that displays the results of the voting app. The result app is built using the Dockerfile in the `result-app` directory.

To build the docker image for the result app and give it access (link it) to the PostgreSQL database, run the following command in the `result-app` directory:

```bash
docker run -d --link postgres:db_host -p 8080:80 -e DB_HOST=db_host -e DB_USER=postgres -e DB_PASSWORD=postgres my_frontend_image

```
