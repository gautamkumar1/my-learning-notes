# Definition of Docker

Docker is a tool that helps you run applications in a separate, self-contained environment called a container. This container includes everything your app needs, like code, libraries, and tools, so it works the same on any computer.
Docker makes it easy to run apps without worrying about setup or compatibility issues.

## Why Use Docker? What Problems Does Docker Solve?

### Without Docker

If you want to run a project:

You need to manually clone the repository from GitHub.
Then, install all dependencies required by the project (e.g., Node.js, MongoDB).
If the dependencies are not available or incompatible with your system, you won't be able to run the project.
With Docker

### Docker solves this problem by:

Allowing developers to package the project with all its dependencies into a Docker image.
This image can then be used to create Docker containers that run the application.
Users don’t need to worry about installing dependencies like Node.js or MongoDB because the Docker container already includes them.
This ensures the project runs consistently, regardless of the host system.

## Difference Between Containers and Images

### Docker Image

- A Docker image is a blueprint or a template for creating containers.
It contains all the necessary files, code, and dependencies needed to run an application.
Images are immutable—once created, they cannot be changed. If modifications are needed, a new version of the image is created (e.g., image:v1, image:v2).
Example:

- Think of an image as a recipe. You can’t directly use it, but you can follow it to create a dish (a container).
Docker Container

- A Docker container is a running instance of a Docker image.
It is where your application runs, based on the blueprint (image).
Containers are lightweight, isolated, and can be started, stopped, or deleted without affecting the image.
Example:

- Think of a container as the actual dish made from the recipe (image). You can eat it, modify it while running, or throw it away and recreate it using the same recipe.

### How Docker Pulls Images (Example: docker run hello-world)

- Local Search:
Docker first checks if the hello-world image exists on your local system.
Pull from Docker Hub:
If the image is not found locally, Docker automatically downloads (pulls) it from Docker Hub.
Run Container:
After pulling, Docker creates a container from the hello-world image and runs it.
This process ensures that the required image is always available, even if it’s not already on your system.

### Why Can’t You Delete a Docker Image While Its Container Is Running?

- Images are the base for containers.
If a container is running, it relies on the image to operate. Docker prevents you from deleting the image while it’s in use to avoid breaking the running container.

- You must stop and remove the container before deleting the image.

```Commands 

Stop the Container: docker stop <container_id>
Remove the Container: docker rm <container_id>
Delete the Image: docker rmi <image_id>
```
#### How Docker Pull Works

1. Docker Pull Without Version Specified
```
When you run:

docker pull node

Docker will pull the latest version of the node image from Docker Hub.
If you've already pulled a version of the node image previously, Docker checks if there’s any layer that has been updated or is missing. It only downloads the missing layers (i.e., only the new parts that are not already on your system).
This layered system helps in optimizing image downloads, as Docker doesn’t need to download the entire image again.
```

2. Docker Pull With Specific Version
```
If you want to pull a specific version of the Node.js image, you can specify the tag (version) like this:

docker pull node:<version>

Note - This process applies to all images, not just node, meaning you can specify different tags for any image to control which version you want to pull.
```

### Docker Commands Explained

#### Run in Detached Mode (-d )

```docker run -d node```

This runs the node image in detached mode, meaning the container will keep running in the background, even if you close the terminal.
Detached mode is useful for running long-lived services like web servers or APIs.

#### Run and Assign a Name to the Container

```docker run --name my-node-container node```

The --name option lets you assign a custom name (my-node-container in this example) to the container.
This makes it easier to manage and identify containers, instead of relying on Docker's default randomly generated names.

#### Port Mapping ( -p )

```docker run -p 3000:3000 -e PORT=3000 node```

The -p flag maps a port from the container to your local system.

First port (3000): Port on your local system/browser.

Second port (3000): Port inside the container that the app listens to.

The -e flag is used to pass environment variables to the container.
Example: PORT=3000 sets the PORT variable inside the container.

#### Using an Environment File

```docker run --env-file ./path/to/envfile node```

Instead of manually passing multiple environment variables using -e, you can use the --env-file flag to provide a file containing all the variables.

#### Listing Docker Images

docker images

This command lists all the Docker images available on your system, along with their:

Repository name (e.g., node).
Tag (e.g., 16, latest).
Image ID (unique identifier).
Size of the image.

#### Seeing running docker
```docker ps```

### How to Build and Run a Docker Image with Proper Networking

#### Build the Docker Image
The command to build a Docker image with a specific name and tag is:

```docker build -t <image-name>:<tag> .```

```<image-name>:``` The name you want to give your image. Example: my-app.
```<tag>:``` The version tag for the image. Example: latest or v1.0.
.: Indicates the directory where the Dockerfile is located.

### Run the Docker Image with an Environment File

Once the image is built, run a container using the following command:

- ```docker run --name <container-name> --env-file <env-file-path> -p <host-port>:<container-port> <image-name>:<tag>```

- --name ```<container-name>:``` Assigns a name to the container (e.g., my-app-container).
- --env-file ```<env-file-path>:``` Specifies the path to your environment file (e.g., ./config.env).
- ```-p <host-port>:<container-port>:``` Maps the port from your local system to the container.
- ```<image-name>:<tag>:``` Specifies the image name and version tag to use.

```docker run --name my-app-container --env-file ./config.env -p 3000:3000 my-app:v1.0```

#### Expose MongoDB Port
MongoDB typically runs on port 27017. When running the MongoDB container, expose the port like this:

```docker run -d --name mongo-container -p 27020:27017 mongo```

- 27020: Port on your local machine.

- 27017: Default MongoDB port inside the container.

- Network Error Solution: Use the Same Network

- By default, containers are isolated from each other. If one container (e.g., your application) needs to connect to another (e.g., MongoDB), they must be on the same Docker network.

#### Create a Docker network:

```docker network create my-network```

- Run MongoDB on the custom network:

```docker run -d --name mongo-container --network my-network mongo```

- mongo-container - This is mongo container that is you provided when u created a mongo image.

- my-network - This is a name of network that you created.

## Important Notes

- Networking: If you need your app to connect to a MongoDB container, ensure both are on the same Docker network. Example:

- docker network create my-network
- docker run -d --name mongo-container --network my-network mongo
- docker run -d --name my-app-container --network my-network --env-file ./- config.env -p 3000:3000 my-app:v1.0

## Why Using localhost in MONGO_URI Fails Inside a Docker Container

- When you use localhost in MONGO_URI (e.g., mongodb://localhost:27017/mydatabase), it refers to the container's internal network, not your host machine or another container. This causes a connection failure because:

- MongoDB is running in a separate container.
Your app container cannot resolve localhost to the MongoDB container.

### Correct Way to Connect to MongoDB in Docker

- Pass the MongoDB Container Name in MONGO_URI Instead of localhost, use the name of the MongoDB container:

- MONGO_URI=mongodb://mongo-container:27017/mydatabase

- Here:
mongo-container is the name of the MongoDB container.
Both containers must be on the same Docker network to communicate.

## Key Points
- Use the container name (mongo-container) in MONGO_URI instead of localhost.
- Both the app and MongoDB containers must be on the same Docker network.

- Creating a Dockerfile is essential to containerize an application. It contains instructions to build a Docker image. 

Here's a step-by-step guide on how to create a Dockerfile:

```
Steps to Create a Dockerfile

# Step 1: Use a base image
# Use the official Node.js image. If no version is specified, the latest version will be used.
FROM node:<version> 

# Step 2: Set the working directory
# Sets the working directory inside the container to /app
WORKDIR /app

# Step 3: Copy package.json and package-lock.json
# Copy only package.json and package-lock.json to hold caching during builds
COPY package*.json ./
# This ensures `npm install` only runs when there are changes in these files, improving build time.

# Step 4: Install dependencies
RUN npm install

# Step 5: Copy the rest of the application code
# Copies all remaining application files to the working directory
COPY . .

# Step 6: Expose the application port
# This makes port 3000 accessible to the host machine
EXPOSE 3000

# Step 7: Define the command to start the application
# Starts the application using npm
CMD ["npm", "start"]
```

## Key Notes

- Why Use .dockerignore?

Ans : Prevents unnecessary files (e.g., node_modules, .git, .env) from being added to the Docker image.
Reduces the final image size.
Speeds up the build process.

- Why Copy package*.json Separately?

Ans: Docker caches the RUN npm install layer.
If the package.json or package-lock.json files haven’t changed, Docker skips npm install, saving build time.

Expose MongoDB Ports

If you are running MongoDB as a separate container, ensure the MongoDB container and the app container are on the same network.

Here's a docker-compose.yml file to build and run Docker images for both a client and a server, assuming each has its own Dockerfile.



```
version: '3.8'

services:
  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    image: client-app-image:1.0
    container_name: client-app-container
    ports:
      - "3000:3000"
    networks:
      - app-network

  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    image: server-app-image:1.0
    container_name: server-app-container
    ports:
      - "5000:5000"
    environment:
      - PORT=5000
      - MONGO_URI=mongodb://mongo-container:27017/mydatabase
    depends_on:
      - mongo
    networks:
      - app-network

  mongo:
    image: mongo:latest
    container_name: mongo-container
    ports:
      - "27017:27017"
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```


- Build the Services

```docker-compose build```

- Start the Containers

```docker-compose up```

- Stop the Containers

```docker-compose down```


## What is a Volume in Docker?

A volume in Docker is a way to store and share data between your host machine (your computer) and a Docker container. It allows data to persist even if the container is stopped or deleted.
What Happens If You Don’t Use Volumes?

- Data Loss: If you don't use volumes and delete a container, all the data stored inside the container is permanently lost.
Difficult Development Workflow

- Without a volume, every time you update your code or files, you would need to rebuild the Docker image and restart the container.
Hard to Share Data Between Containers

- Sharing data between containers without volumes is complicated and inefficient.

## Why Use Volumes?

- Data Persistence: By default, any data created inside a container will be lost if the container is deleted.
A volume ensures that your data remains intact even after the container is removed.

-  Development: Volumes allow you to edit files on your host machine, and the changes will immediately reflect in the container. This is helpful during development.

- Data Sharing: You can use volumes to share files between multiple containers, making them work together easily.

## Key Takeaways

- Volumes save your data, even if the container is deleted.

- They make development faster by allowing live changes to files.

- They enable data sharing between containers and the host machine.

- Without volumes, your data is temporary and will disappear when the container is removed.

## Docker provides three primary ways to add volumes to containers. 

1. Anonymous Volumes
What It Is: Docker creates a volume automatically, but you don’t give it a specific name.
Use Case: When you just need to store temporary data without caring about its name or location on the host.

2. Named Volumes
What It Is: You explicitly give a name to the volume, and Docker manages it for you.
Use Case: When you want to persist data and reuse the volume across containers.

3. Bind Mounts
What It Is: Links a directory or file from your host machine to a specific path inside the container.
Use Case: When you want live updates in the container as you edit files on the host.

#### Note - To check which database file stores the data while persisting, we can search for it on Google.
```
Docker Compose yml file with Volumes

version: '3.8'

services:
  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    image: client-app-image:1.0
    container_name: client-app-container
    ports:
      - "3000:3000"
    volumes:
      - client-data:/app  # Named volume to persist client-side data
      - ./client:/app     # Bind mount for live development
    networks:
      - app-network

  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    image: server-app-image:1.0
    container_name: server-app-container
    ports:
      - "5000:5000"
    environment:
      - PORT=5000
      - MONGO_URI=mongodb://mongo-container:27017/mydatabase
    volumes:
      - server-data:/app  # Named volume to persist server-side data
      - ./server:/app     # Bind mount for live development
    depends_on:
      - mongo
    networks:
      - app-network

  mongo:
    image: mongo:latest
    container_name: mongo-container
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db # Named volume to persist MongoDB database data
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  client-data:
    driver: local
  server-data:
    driver: local
  mongo-data:
    driver: local
```

### What is Watch Mode?

Watch mode is a feature commonly used in development environments that continuously monitors your project's files for changes. When a change is detected (e.g., a file is updated, added, or deleted), the application or service automatically restarts or rebuilds to reflect those changes.
```
Example with Watched Mode

version: '3.8'

services:
  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    image: client-app-image:1.0
    container_name: client-app-container
    ports:
      - "3000:3000"
    volumes:
      - client-data:/app          # Named volume to persist client-side data
      - ./client:/app             # Bind mount for live development
      - /app/node_modules         # Ignore node_modules to avoid conflicts
    command: ["npm", "run", "dev"]  # Start the client in watch mode (e.g., React/Vite)
    networks:
      - app-network

  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    image: server-app-image:1.0
    container_name: server-app-container
    ports:
      - "5000:5000"
    environment:
      - PORT=5000
      - MONGO_URI=mongodb://mongo-container:27017/mydatabase
    volumes:
      - server-data:/app          # Named volume to persist server-side data
      - ./server:/app             # Bind mount for live development
      - /app/node_modules         # Ignore node_modules to avoid conflicts
    command: ["npx", "nodemon", "index.js"]  # Start the server in watch mode
    depends_on:
      - mongo
    networks:
      - app-network

  mongo:
    image: mongo:latest
    container_name: mongo-container
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db       # Named volume to persist MongoDB database data
    networks:
      - app-network

networks:
  app-network:
    driver: bridge

volumes:
  client-data:
    driver: local
  server-data:
    driver: local
  mongo-data:
    driver: local
```
