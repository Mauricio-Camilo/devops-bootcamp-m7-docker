# Demo Project

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
  



