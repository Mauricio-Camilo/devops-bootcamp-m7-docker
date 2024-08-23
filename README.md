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
  
  docker run -d \
-p 27017:27017 \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=password \
--net mongo-network \
--name mongodb \
mongo

  docker run -d \
  -p 8081:8081 \
  -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
  -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
  -e ME_CONFIG_MONGODB_SERVER=mongodb \
  -e ME_CONFIG_MONGODB_AUTH_DATABASE=admin \
  --net mongo-network \
  --name mongo-express \
  mongo-express

- Configuring database and test application

  Inside the UI of Mongo-Express, a database called user-account and a collection called users were manually created. The application was configured to connect to this database using MongoClient, and there were two endpoints: one to update the user profile data and another to retrieve the profile. The application was tested in the browser (localhost:3000) by updating the user profile. After this, it was possible to verify in the Mongo-Express UI that the data had been updated by the application, showing that the connection between the app and the database was successfully established.

# Demo Project 2

Docker Compose - Run multiple Docker containers

## Technologies Used

Docker, MongoDB, MongoExpress

## Project Description

- Write Docker Compose file to run MongoDB and MongoExpress containers

### Details of project  
  
- Configuring Docker Compose file

  In this project, a Docker Compose file in YAML format was created to simplify the process of running the containers. All the information that 
  was used to write the docker run commands is now consolidated into a single file. Both the MongoDB and Mongo-Express containers have their own 
  sections in the file. One advantage of using Docker Compose is that there is no need to manually create the network; when Docker Compose starts, 
  a network is automatically created, connecting the containers. Additionally, the Docker Compose file was configured to ensure that Mongo-Express 
  will only start after the MongoDB container is ready to accept connections.

- Run the images

  After configuring the Docker Compose file, the command docker compose up was used to create the containers. By running the Node.js app again, the same steps were followed to test the application (creating the database and collection in the Mongo-Express UI). This was necessary because the containers created by the docker run commands were not configured to persist data, so the new containers launched by Docker Compose did not retain any previous data. To stop all the containers and the network, the command docker compose down eas runned.


