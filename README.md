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



