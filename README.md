## demo app - developing with Docker

This demo app shows a simple user profile app set up using

- index.html with pure js and css styles
- nodejs backend with express module
- mongodb for data storage

All components are docker-based

### With Docker

#### To start the application

Step 1: Create docker network

    docker network create mongo-network

Step 2: start mongodb

    docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo

Step 3: start mongo-express

    docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express

_NOTE: creating docker-network in optional. You can start both containers in a
default network. In this case, just emit `--net` flag in `docker run` command_

Step 4: open mongo-express from browser

    http://localhost:8081

Step 5: create `user-account` _db_ and `users` _collection_ in mongo-express

Step 6: Start your nodejs application locally - go to `app` directory of project

    npm install
    node server.js

Step 7: Access you nodejs application UI from browser

    http://localhost:3000

### With Docker Compose

#### To start the application

Step 1: start mongodb and mongo-express

    docker-compose -f docker-compose.yaml up

_You can access the mongo-express under localhost:8080 from your browser_

Step 2: in mongo-express UI - create a new database "my-db"

Step 3: in mongo-express UI - create a new collection "users" in the database
"my-db"

Step 4: start node server

    npm install
    node server.js

Step 5: access the nodejs application from browser

    http://localhost:3000

#### To build a docker image from the application

    docker build -t my-app:1.0 .

The dot "." at the end of the command denotes location of the Dockerfile.

# My NOTES

### Reference: https://www.youtube.com/watch?v=3c-iBn73dDE

What a container concept is? A way to package application with all the necessary
dependencies and configuration.

- This packages is portable artifact, easily shared and moved around between the
  dev team or development and operations team. And that portability of
  conatiners plus everything packaged in one isolated env gives it some of the
  advantages that makes development and deployment process more efficient.

Where do containers live? they live in container repository

- Types of container repos: private, e.g: for companies & public like on Docker
  Hub (hub.docker.com) you can find more than a hundred thousand container
  images of diff apps hosted or stored in this docker hub repo, and for every
  app there is an official docker container or container image.

Application development:

- Before Containers:- installation process different on each OS evnironement.-
  Many steps where something could go wrong.

- After containers:- It’s an isolated OS layer (own isolated environment) with
  linux based image. (You have everything packaged in one isolated env).
- One command to install the app.
- Run same app with 2 or more different versions

Application Deployment:

- Before Containers:- traditional deployment process will look like this:
  Development team will produce artefacts together with a set of instructions of
  how to actually install and configure those artifacts on the server. So you
  would have a jar file or something similar for your application, And in
  addition, you would have some kind of a DB service or some other service also
  with a set of instructions of how to configure and set it up on the server. So
  dev team would give those artifacts over to the operations team and the ops
  team will handle setting up the env to deploy those apps.
- The problem with this approach: - You have to install & configure everything
  directly on the OS, which may lead to conflicts of dependencies versions. And
  the other problem may be some misunderstandings between Dev & Ops team,
  because everything is in a textual guide of deployment as instructions, and
  the Ops team, may need to go back to the dev team to ask for more details
  (back & forth communication until the app is successfully deployed on the
  server). ![](./notes-screenshots/1.png)

- After containers: this process is actually simplified because now you have the
  devs and ops working in one team to package the whole configuration deps
  inside the app. - No env configuration is needed on the server, except Docker
  runtime.- You only need to do is to run a docker command that pulls that
  container image that you’ve stored somewhere in the repo and then run it ￼
  ![](./notes-screenshots/2.png)

What is a container (technically)?

- Container is made up of images, so we have layers of stacked images on top of
  each other. And the base of most of the containers you would have a Linux
  based image (layer) (because it’s small in size). And application image
  (layer) on top

Docker Vs Virtual machines: First we need to know How OS is mad up: OS have 2
layers (OS kernel & Applications layer). Kernel communicates with hardware and
the application run on the kernel layer. ![](./notes-screenshots/3.png)

Both Dockers & VMs they are virtualisation tools, but what they are actually
virtualise?- Docker: virtualise the apps layer (when you download a docker image
it contains the apps layer of OS and some other apps installed on top of it and
it uses the kernel of the host because it doesn’t have its own kernel).

- VM & virtual box: have the apps layer and their own kernel so it virtualises
  the complete OS (which means that when you download a virtual machine image on
  you host, it doesn’t use you host kernel it puts up its own).
- Size of Docker image is much smaller and it starts & run much faster
- Compatibility: VM of any OS can run on any OS host ￼

Container Vs Image:

- Container: is a running env for Image (all the configuration needed to apps in
  the image) and it has a port which binded to it, which makes it possible to
  talk to the app which is running inside of the container. (Also the file
  system is virtual)
- Application Image: postgres, redis, mongo… ￼![](./notes-screenshots/4.png)

Docker commands:

- docker pull = pulls docker image to local env
- docker ps <-a> = list running containers and -a will gives you all the
  containers no matter they are currently running or not
- docker run <-d> = pull the image and starts a new container for it and runs it
  with detach mode so you can use a terminal again to run it again after
  stopping it
- docker stop = stops the container.
- docker start <containerID> = start stopped container
- docker run -p<host_port>:<container_port> —name <alias_name> <image_name> =>
  docker run -p6000:6379 redis_latest redid
- docker logs <containerID> or <image_name> = show all logs for the container
  (for debugging)
- docker exec -it <containerID> /bin/bash OR /bin/sh (to navigate inside the
  container directory and see what is going on also for debugging purposes).
- Docker network create <name_of_network> in order to interact between two or
  more containers

Container Port Vs Host Port:

- You must bind one container to only one port on your host machine
  ￼![](./notes-screenshots/5.png)

A real-life example of workflow with Docker: ￼ ￼![](./notes-screenshots/6.png)

Example of running a mongo IMAGE with overwritten name mongodb and in
mongo-network with username&password command: (Start mongodb)

> docker run -d \
> -p27017:27017 \
> -e MONGO_INITDB_ROOT_USERNAME=admin \
> -e MONGO_INITDB_ROOT_PASSWORD=password \
> —name mongodb \
> --net mongo-network \
> mongo

Example of running a mongo-express image and connecting it to previously created
mongodb container: (start mongo-express)

> docker run -d \
> -p 8081:8081 \
> -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
> -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
> -e ME_CONFIG_MONGODB_SERVER=mongodb \
> --net mongo-network \
> --name mongo-express \
> mongo-express

DOCKER COMPOSE: take the whole command with its configuration and Map it into a
file so that we have a structured commands, so for example if we have 10
containers that we want to run for your application and they all need to talk to
each other and interact with each other, you can basically write all the run
commands for each container in a structured way in the docker compose file:
Example: mongo-docker-compose.yaml file

version: ‘3’ // latest version of docker compose services: mongodb: //
container-name image: mongo // <image-name> ports: - 27017:27017 //
HOST:CONTAINER environment: - MONGO_INITDB_ROOT_USERNAME=admin -
MONGO_INITDB_ROOT_PASSWORD=password

NOTE: No need to write the docker-network because docker-compose takes care of
creating a common network

Command for docker-compose: Run: docker-compose -f <docker-compose.yaml file
name> up Shutdown: docker-compose -f <docker-compose.yaml file name> down

DOCKERFILE: is a blueprint for creating/building docker images. // search for
node image in docker hub The file should be named: Dockerfile // case-sensitive

Example of docker file:

FROM node // first line of every docker file is from image because our app is
built on nodes backend ENV // optional to set env variables but it’s preferred
to put them in the docker-compose file RUN mkdir -p /home/app ======> RUN:
execute any Linux command COPY <source> <destination> e.g: COPY ./app /home/app
==> copy current folder files to home/app ==> COPY executes on the HOST machine
CMD [“node, “home/app/server.js”] ==> start the app with: “node server.js”

CMD Vs RUN CMD: entrypoint command RUN: ya can have multiple RUN commands ￼

￼ ￼![](./notes-screenshots/7.png)￼ ￼![](./notes-screenshots/8.png)

Build Docker image from that Docker file: Command: docker build -t
<image-name>:<tag> <path-of-docker-file> Example: docker build -t my-app:1.0 .
======> . means the same location Command: docker images // to see your images

What Jenkins does: it take the docker file that we created, so we have to commit
the docker file into the repo with the code, and then it will build a docker
image based on the docker file.

You have to share the docker image in order to let the other team members can
pull and use it, then I have to push it to a docker repo and from there either
people can take it for example a tester maybe want to download the image from
there and test it locally, or a development server can actually pull it from
there. ￼ ￼ ￼![](./notes-screenshots/9.png)￼￼ ￼![](./notes-screenshots/10.png)￼

Command: docker run my-app:1.0

NOTE: when you adjust the Dokcerfile, you MUST rebuild the Image And to do that
=> Command: docker rmi <imageID> // if you can’t because it’s in use then stop
and delete the container first of this image then delete the image

Then after your adjustments run: docker build -t my-app:1.0 .

PRIVATE DOCKER REPOSITORY: ￼

Create private docker reopo on AWS: ECR: Elastic container registry:

- Create an ECR private repo
- Click on View push commands ￼ ￼ ￼
- So copy the first command which is the docker login command to AWS // login
  command runs once not every time you want to make changes - Prerequisites: ￼
- Check how to configure these prerequisites.
- Image naming concepts in docker registries: - registryDomain/imageName:tag -
  So For example in DOCKER HUB: docker pull mongo:4.2 this is shorthand for
  docker pull docker.io/ibrary/mongo:4.2 - And In AWS ECR: docker pull
  664574038682.dkr.ecr.eu-central-1.amazonaws.com/my-app:1.0 - So the next step
  is to tag the image ￼
- After executing this line we will have 2 identical images with different names
  ￼
- The next step is to run push command to push the local image to AWS ECR:
- Command: docker push
  664574038682.dkr.ecr.eu-central-1.amazonaws.com/my-app:1.0 ￼ ￼

Make some changes to the App, rebuild and push a new version to AWS repo:

- Do you changes to the code or the docker file or anything.
- Run build command: docker build -t my-app:1.1 // here increment the tag
- Run Tag command of your image: docker tag my-app:1.1
  664574038682.dkr.ecr.eu-central-1.amazonaws.com/my-app:1.1
- Run push command: docker push
  664574038682.dkr.ecr.eu-central-1.amazonaws.com/my-app:1.1 ￼ NOTE: if you use
  Jenkins then it needs login credentials as well. ￼

Deploying the containerised application: ￼ ￼ ￼

- Add the app part into the mongo.yaml file
- Change the url in APIS to be mongodb://admin:password@mongodb instead of
  mongodb://admin:password@localhost:27017 because they are in the same file now
  and can be connected to the containers configured in the same file for example
  mongoldb container so we can call it with its name directly.

Docker volumes for data persistence to be used for DBs or other stateful apps ￼
￼

￼ Then the data gets automatically replicated.

3 Volume types: usually using docker run command

- docker run 1. -v <host-dir:container-dir> eg. -v
  /home/mount/data:/var/lib/mysql/data // this is where we define the connection
  or the reference between the host directory and the container directory and
  this type of volume definition called (HOST VOLUMES). So, you decide where in
  the host file system the reference is made 2. -v <container-dir> // just
  referencing the container directory so you don’t specify which directory on
  the host should be mounted but, that’s taking care of the docker itself. So,
  that directory is first of all automatically created by Docker under the varly
  docker volumes (for each container a folder is generated that gets mounted
  automatically to the container) and this type called (ANONYMOUS VOLUMES)
  because you don’t have a reference to this automatically generated folder 3.
  -v name:/var/lib/mysql/data // it’s an improvement of the anonymous volumes
  and it specifies the name of the folder on the host file system and the name
  is up to you, it’s just to reference the directory and that type of volumes
  called (NAMED VOLUMES). So you can reference the volume by name. And this is
  the most preferred one to be used in production ￼
- You have to list all the volumes defined in the docker-compose file at the end
  of the file ￼
- This is beneficial if you want to create volumes for different containers, you
  would list them all at the end of the file and on the container level then you
  actually define under which path that specific volume can be mounted.
- And the benefit of that is that you can actually mount a reference of the same
  folder on the host to more than one containers and that would be beneficial if
  those containers need to share data. In this case, you would want the same
  name or reference to two different containers and you can mount them into
  different path inside the container even.

- To get info about the volumes it’s in /var/lib/docker/volumes
- Command: screen
  ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
  ￼
