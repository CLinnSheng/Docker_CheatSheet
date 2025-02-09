# Docker Cheatsheet

## Introduction
Docker is a virtualization software that makes developing and deploying applications much easier and faster. It packages applications with all necessary dependencies, configuration, system tools, and runtime. In essence, it provides a standardized unit containing everything an application needs to run.

## Development Process

### Before Docker
* Developers needed to install and configure all services directly on their OS in their local machine
* Installation processes differed for each OS environment
* Setup required numerous manual steps

### With Docker
* Applications run in isolated environments
* Each container is packaged with all dependencies and configurations
* Services start with a single Docker command
* Commands remain consistent across all operating systems
* Process is standardized for any local development environment

## Deployment Process

### Before Docker
* Developers provided artifacts/packages (JAR, etc.) and instructions to the Ops team through documentation
* Ops team handled installation and configuration of apps and dependencies on the server OS

### With Docker
* All configurations and dependencies are packaged inside the Docker artifact
* Minimal server-side configuration required

## Docker vs Virtualization

### Operating System Layers
1. OS Application Layer
2. OS Kernel
   * Core of every operating system
   * Manages hardware and software component interactions
3. Hardware

### Virtualization Comparison

#### Docker
* Virtualizes the OS Application Layer
* Contains services and apps installed on top of the layer

#### Virtual Machine
* Virtualizes both OS Application Layer and OS Kernel
* Uses its own OS instead of the host OS

#### Key Differences
* Docker is lighter weight and faster than VMs
* VMs offer broader OS compatibility than Docker

## Docker Components

### Docker Images
* Executable application artifacts
* Include:
  * Application source code
  * Complete environment configuration
  * OS Layer
  * Environment variables
  * Directory structures and files

### Docker Containers
* Runtime instances of Docker images
* Multiple containers can run from a single image
* Each container operates independently

## Port Mapping
* Host machines have limited available ports
* Multiple containers can run on a single host
* Port conflicts must be managed through proper mapping

## Essential Docker Commands
### Basic Image Commands
```bash
# List all images available locally
docker images

# Pull an image from Docker Hub
docker pull IMAGE_NAME[:TAG]

# Remove an image
docker rmi IMAGE_ID|IMAGE_NAME

# Create & start a new container
docker run [OPTIONS] IMAGE
  -d                   # Run container in background (detached mode)
  -p HOST:CONTAINER    # Map host port to container port
  --name CUSTOM_NAME   # Assign a name to the container
  -v HOST_DIR:CONT_DIR # Mount a volume (HOST VOLUMES - decide where on the host file system the reference is mad)
  -v CONT_DIR          # (Annoymous Volumes) For each container a folder is generate that gets mounted where host directory is automatically created by Docker
  -v name:/CONT_DIR    # (Named Volumes) Reference the volume by name
  -e KEY=VALUE         # Set environment variables
  --net NETWORKNAME    # Network

# List containers
docker ps              # List running containers
docker ps -a           # List all containers (including stopped)

# Container Operations
docker start CONTAINER_ID|CONTAINER_NAME    # Start a stopped container
docker stop CONTAINER_ID|CONTAINER_NAME     # Stop a running container
docker restart CONTAINER_ID|CONTAINER_NAME  # Restart a container
docker rm CONTAINER_ID|CONATINER_NAME       # Remove a container

# View container logs
dokcer logs CONTAINER_ID|CONTAINER_NAME
docker logs -f CONTAINER_ID|CONTAINER_NAME  # Follwo log output

# Container details
docker inspect CONTAINER_ID|CONTAINER_NAME

# Container resource usage
docker stats CONTAINER_ID|CONTAINER_NAME

# Execute command in running container
docker exec -it CONTAINER_ID|CONTAINER_NAME /bin/bash  # Access container shell

# Copy files between host and container
docker cp FILE_PATH CONTAINER_ID|CONTAINER_NAME:CONTAINER_PATH  # Host to container
docker cp CONTAINER_ID|NAME:CONTAINER_PATH FILE_PATH  # Container to host

# List networks
docker network ls

# Create a network
docker network create NETWORK_NAME

# Connect container to network
docker network connect NETWORK_NAME CONTAINER_ID|CONTAINER_NAME

# Build image from Dockerfile
docker build -t IMAGE_NAME:TAG PATH # Whenever you edit the Dockerfile, you have to rebuild the image

# View layer history of image
docker history IMAGE_NAME

# Remove unused resources
docker system prune         # Remove all unused containers, networks, images
docker system prune -a      # Remove all unused resources including unused images
docker container prune      # Remove all stopped containers
docker volume prune         # Remove all unused volumes
```

## Structure of Dockerfile
* Dockerfile start from a parent image or "base image"
* It'a a Docker image that your image is based on
* Dockerfiles **must begin** with a **FROM** instructions
* **FROM**
  * Build this image from the specified image 
* **COPY**
  * Copy the directory into the container
* **ENV**
  * Setting up the environment (prefer on dot yaml file)  
* **WORKDIR**
  *  Sets the working directory for all following Commands
  *  Like changing directory "cd.."
* **RUN**
  * Execute any command in a shell inside the container environment
* **CMD**
  * Instruction to be executed when a Docker container starts
  * Can **only be one CMD instruction** in a Dockerfile

## Structure of DockerCompose.yaml
* Automate the process of running multiple docker container
* **Docker Compose takes care of creating a common Network**
```bash
version:'3'
services:
    mongodb: # Container name
        image: mongo # Image name
        ports:
            - 27017:27017
        volumes:
            - db-data:/var/lib/mysql/data
        environment: # Envrionment
            - MONGO-INITDB_ROOT_USERNAME=admin
            - MONGO-INITDB_ROOT_PASSWORD=password
    mongo-express:
        image: mongo-express # Image name
        ...

# List all the volumes had defined that want to mount into the container
volumes:
    db-data:
        driver: local # Tell the docker to create 1 in host
        # Docker Volume Locations
        # Linux/MacOS: /var/lib/docker/volumes
```
```bash
docker-compose -f FILENAME.yaml up   # Booting all the container
docker-compose -f FILENAME.yaml down # Shutting down all the contianer & network
```
