# Demo Project 1

Use Docker for local development

## Technologies Used

Docker, Node.js, MongoDB, MongoExpress

## Project Description

- Create Dockerfile for Nodejs application and build Docker image
- Run Nodejs application in Docker container and connect to
- MongoDB database container locally.
- Also run MongoExpress container as a UI of the MongoDB database.

### Details of project

- Clone repository

  The first step was cloning the repository and running the JavaScript application using the commands npm install and node server.js inside the app folder.

- Pull images from Docker Hub

  The images for MongoDB and Mongo-Express were pulled from Docker Hub to be used in this demo.

- Network and image run configuration

  A network called mongo-network was created using the command: docker network create mongo-network. It is used to connect all the containers to the same network, allowing communication between them. After this, the MongoDB and Mongo-Express containers were run on their respective ports, with environment variables for login and password, inside the network, using the commands:
  
  ```
  docker run -d \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=password \
  --net mongo-network \
  --name mongodb \
  mongo
  ```
  ```
  docker run -d \
  -p 8081:8081 \
  -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
  -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
  -e ME_CONFIG_MONGODB_SERVER=mongodb \
  -e ME_CONFIG_MONGODB_AUTH_DATABASE=admin \
  --net mongo-network \
  --name mongo-express \
  mongo-express
  ```

- Configuring database and test application

  Inside the UI of Mongo-Express, a database called user-account and a collection called users were manually created. The application was configured to connect to this database using MongoClient, and there were two endpoints: one to update the user profile data and another to retrieve the profile. The application was tested in the browser (localhost:3000) by updating the user profile. After this, it was possible to verify in the Mongo-Express UI that the data had been updated by the application, showing that the connection between the app and the database was successfully established.

  ![Diagram](./images/mongo-ui.png)

# Demo Project 2

Docker Compose - Run multiple Docker containers

## Technologies Used

Docker, MongoDB, MongoExpress

## Project Description

- Write Docker Compose file to run MongoDB and MongoExpress containers

### Details of project  
  
- Configuring Docker Compose file

  In this project, a Docker Compose file in YAML format was created to simplify the process of running the containers. All the information that was used to write the docker run commands is now consolidated into a single file. Both the MongoDB and Mongo-Express containers have their own sections in the file. One advantage of using Docker Compose is that there is no need to manually create the network; when Docker Compose starts, a network is automatically created, connecting the containers. Additionally, the Docker Compose file was configured to ensure that Mongo-Express will only start after the MongoDB container is ready to accept connections.

    ```
    version: '3'
    services:
      mongodb:
        image: mongo
        ports:
         - 27017:27017
        environment:
         - MONGO_INITDB_ROOT_USERNAME=admin
         - MONGO_INITDB_ROOT_PASSWORD=password
      mongo-express:
        image: mongo-express
        restart: always
        ports:
         - 8081:8081
        environment:
         - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
         - ME_CONFIG_MONGODB_ADMINPASSWORD=password
         - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
         - "mongodb"
    ```
    
- Run the images

  After configuring the Docker Compose file, the command docker compose up was used to create the containers. By running the Node.js app again, the same steps were followed to test the application (creating the database and collection in the Mongo-Express UI). This was necessary because the containers created by the docker run commands were not configured to persist data, so the new containers launched by Docker Compose did not retain any previous data. To stop all the containers and the network, the command docker compose down eas runned.

  ![Diagram](./images/docker-compose.png)

# Demo Project 3

Dockerize Nodejs application and push to private Docker Registry

## Technologies Used

Docker, Node.js, Amazon ECR

## Project Description

- Write Dockerfile to build a Docker image for a Nodejs application
- Create private Docker registry on AWS (Amazon ECR)
- Push Docker image to this private repository

### Details of project  
  
- Create a repository in ECR

  In this project, an AWS account was used to push Docker images from previous projects to a service called Elastic Container Registry. In this service, a private repository was created, which will contain only the Docker images from the Node app.

- Steps to push the image

  A list of commands has been provided by ECR to facilitate the pushing process:

  1- Authenticate using the docker login command. This step is also important if another service, such as Jenkins, needs to connect to the repository.
  ```
  aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 808826729764.dkr.ecr.us-east-1.amazonaws.com
  ```
  2- Build the image with a specific name, in this case, the name of the repository.
  ```
  docker build -t my-app .
  ```
  3- Rename the image to include the repository name before the image name. This is necessary because, by default, the docker push command will push the image to Docker Hub. For this reason, it is essential to specify the container name.
  ```
  docker tag my-app:1.0 808826729764.dkr.ecr.us-east-1.amazonaws.com/my-app:1.0
  ```
  4- Push the image to ECR repository.
  ```
  docker push 808826729764.dkr.ecr.us-east-1.amazonaws.com/my-app:1.0
  ```
  Using these commands, the images are successfully pushed to Amazon ECR. It can be a good practice to push the same images with different tags to test various versions of the image.

  ![Diagram](./images/ecr.png)
  

# Demo Project 4

Deploy Docker application on a server with Docker Compose

## Technologies Used

Docker, Amazon ECR, Node.js, MongoDB, MongoExpress

## Project Description

- Copy Docker-compose file to remote server
- Login to private Docker registry on remote server to fetch our app image
- Start our application container with MongoDB and MongoExpress services using docker compose

### Details of project  
  
- Update Docker-compose file and node application

  In this project, an update was made in docker-compose to add the node-app docker image pushed in ECR. 

    ```
    services:
      my-app:
        image: 808826729764.dkr.ecr.us-east-1.amazonaws.com/my-app:1.0
        ports:
         - 3000:3000
    ```
  Now the my-app container will run on the same network as mongo-db and mongo-express. However, it is necessary to update the connection that the   Node app makes to MongoDB.

  ```
  // use when starting application locally with node command
  let mongoUrlLocal = "mongodb://admin:password@localhost:27017";

  // use when starting application as docker container, part of docker-compose
  let mongoUrlDockerCompose = "mongodb://admin:password@mongodb";
  ```
  By analyzing the code above, the connection was initially made to localhost, but with the changes in Docker Compose, the Node app can now connect using the name of the mongodb container. Additionally, the MongoClient connection in the application was updated to use the mongoUrlDockerCompose variable. The new version of the application was pushed to ECR again with the name my-app:1.0 to be recognized by Docker Compose.

- Run docker compose in remote server

  Considering that the new Docker image was successfully pushed, the Docker Compose file was created on the remote server. After that, you need     to log in to the private Docker registry on the remote server to fetch our app image. By following these steps and using the docker compose up    command, all the containers will be running on the new server. You can then repeat the steps from the previous projects to test the application.

# Demo Project 5

Persist data with Docker Volumes

## Technologies Used

Docker, Node.js, MongoDB

## Project Description

- Persist data of a MongoDB container by attaching a Docker volume to it 

### Details of project  
  
- Update Docker-compose file with volume configuration

  In this project, an update was made in docker-compose to add the volume o persist data.

    ```
      mongodb:
        image: mongo
        ...
        volumes:
         - mongo-data:/data/db
    
    volumes:
      mongo-data:
        driver: local
    ```
  A new volume named mongo-data was created on the host, which represents physical storage in the local file system. Additionally, within the mongodb service, a new volume section was added to map mongo-data to an internal path of the Docker image. In other words, when Docker Compose runs the container, it will store all data in the host volume mongo-data and map it to the path /data/db. Is is possible to verify that the data in mongo-data is the same as in /data/db.

  ![Diagram](./images/volumes.png)

# Demo Project 6

Create Docker repository on Nexus and push to it

## Technologies Used

Docker, Nexus, DigitalOcean, Linux

## Project Description

- Create Docker hosted repository on Nexus
- Create Docker repository role on Nexus
- Configure Nexus, DigitalOcean Droplet and Docker to be able to push to Docker repository
- Build and Push Docker image to Docker repository on Nexus

### Details of project  
  
- Create Docker hosted repository and role on Nexus

  In this project, a Docker hosted repository was created with a previously created blob store. To allow access to this repository, a role with the privilege nx-repository-view-docker- 
docker-hosted-add was created and assigned to a user.

- Configure Nexus, server and docker

  The next step of the project involves configuring the Nexus repository to allow access via the Docker client. In the docker-hosted settings, the HTTP option was enabled on port 8083, and 
  this port was also allowed in the server’s firewall (inbound rule). This allows the repository to be accessed via the server's public IP on this port.

  Another configuration involves Nexus Realms, which manages the token used for authentication. This step was completed by enabling the Docker Bearer Token Realm option.

  One final consideration is that Nexus is running on an insecure connection, so the Docker client needs to be configured to accept the docker-hosted repository as an insecure connection. 
  Since I am using Ubuntu, this was done by editing the /etc/docker/daemon.json file, adding the URL of the docker-hosted repository, and then restarting Docker.

  ```
   {
	"insecure-registries" : ["http://98.81.158.92:8083"]
    }
  ```
  
  After all these configurations, the Docker client is now ready to establish a connection to the docker-hosted repository in Nexus using docker login. During the first attempt, it prompted 
  for a username and password to validate the login. The token was then saved in ./docker/config.json, allowing future logins to be performed without needing the username and password.

  ![Diagram](./images/docker-login.png)

  With docker login successfully connected, the same commands used in the previous steps to push an image were applied here, renaming the image with the URL of the docker-hosted repository. After pushing the image, it can be observed in Nexus.

  ![Diagram](./images/nexus.png)

# Demo Project 7

Deploy Nexus as Docker container

## Technologies Used

Docker, Nexus, DigitalOcean, Linux

## Project Description

- Create and Configure Droplet
- Set up and run Nexus as a Docker container

### Details of project  
  
- Create new Droplet and install docker

  A new droplet was created to serve as a server for running Nexus inside a Docker container. This involved configuring firewall rules, connecting via terminal, and installing Docker.
  
- Pull Nexus image and configuration

  1- The first step was to create a volume to persist data used in Nexus.
  
    ```
    docker volume create --name nexus-data
    ```
  2- Run Nexus container in port 8081, using the created volume, and pulling the image sonatype/nexus3 from Docker Hub.
  
    ```
    docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
    ```
 - Check docker container and volume

   Is is possible to verify the Nexus container is running by checking with docker ps. It is already set up with a user called nexus.

   ![Diagram](./images/nexus-container.png)

   The mount point created on the server is located at /var/snap/docker/common/var-lib-docker/volumes/nexus-data/_data.

   ![Diagram](./images/nexus-volume.png)

   Inside the container, the volume is mounted in the home directory.

   

  
