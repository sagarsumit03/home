# Docker

A docker image contains the main application, any services that are needed to run/execute the application and the OS layer.  
It contains the environment variables and directories as well.

A docker image is a immutable template, but from one image we can run multiple containers with different config.


## Create a Docker Spring Boot project:

1. create a spring boot application with mongoDb as dependency.
2. added swagger2 UI, that can be accessed as below:  
swaggerUI: `http://localhost:8080/swagger-ui/#/demo-application`  
api docs: `http://localhost:8080/v2/api-docs`
3. add Dockerfile to configure instructions for Docker.
  ```java
    1 FROM openjdk:8-jdk-alpine
    2 ARG JAR_FILE=target/*.jar
    3 COPY ${JAR_FILE} app.jar
    4 ENTRYPOINT ["java","-jar","/app.jar"]
  ```
4. docker commands explained:  
   - Each docker command runs on its own layer.example:
  
       > we first start with a layer of JDK 8  
       then declare a variable as JAR_FILE   
       copy that jar file to app.jar # copy works on Host system, as we are copying the local file which is present
              in target/ folder to app.jar in root folder.  
       astly we define the entrypoint to the app: here we use command: `java -jar ./app.jar` -> this command works in host/local as well.
         
    - FROM: This is the base image, the dockerfile starts with this command.    
                 in our case we are using: `openjdk:8-jdk-alpine`, to run our Spring boot application.  
                 It can be `ubuntu:<tag`> or `node:<tag>` based off what we are trying to create.
    - RUN: runs any command in the containe.
    - COPY: runs a copy command to copy a local file (on our laptop) to the container linux directory.  
             example: `COPY target/*.jar app.jar`  
    - WORKDIR: if we want to cd to a cetain path in container do `WORKDIR <path_to>`
    - CMD: similar to ENTRYPOINT, CMD is to define certain commands to execute, like: `CMD["./catlina.sh","start"]`  
    - Dockerfile should specify at least one of CMD or ENTRYPOINT commands.
    
  
5.  below is how adding CMD in docker, with ENTRYPOINT get executed in order:
    https://stackoverflow.com/questions/21553353/what-is-the-difference-between-cmd-and-entrypoint-in-a-dockerfile


> The ENTRYPOINT specifies a command that will always be executed when the container starts.  
  The CMD specifies arguments that will be fed to the ENTRYPOINT.
```    
    -- No ENTRYPOINT
    
    ╔════════════════════════════╦═════════════════════════════╗
    ║ No CMD                     ║ error, not allowed          ║
    ╟────────────────────────────╫─────────────────────────────╢
    ║ CMD ["exec_cmd", "p1_cmd"] ║ exec_cmd p1_cmd             ║
    ╟────────────────────────────╫─────────────────────────────╢
    ║ CMD ["p1_cmd", "p2_cmd"]   ║ p1_cmd p2_cmd               ║
    ╟────────────────────────────╫─────────────────────────────╢
    ║ CMD exec_cmd p1_cmd        ║ /bin/sh -c exec_cmd p1_cmd  ║
    ╚════════════════════════════╩═════════════════════════════╝

    -- ENTRYPOINT exec_entry p1_entry

    ╔════════════════════════════╦══════════════════════════════════╗
    ║ No CMD                     ║ /bin/sh -c exec_entry p1_entry   ║
    ╟────────────────────────────╫──────────────────────────────────╢
    ║ CMD ["exec_cmd", "p1_cmd"] ║ /bin/sh -c exec_entry p1_entry   ║
    ╟────────────────────────────╫──────────────────────────────────╢
    ║ CMD ["p1_cmd", "p2_cmd"]   ║ /bin/sh -c exec_entry p1_entry   ║
    ╟────────────────────────────╫──────────────────────────────────╢
    ║ CMD exec_cmd p1_cmd        ║ /bin/sh -c exec_entry p1_entry   ║
    ╚════════════════════════════╩══════════════════════════════════╝

    -- ENTRYPOINT ["exec_entry", "p1_entry"]

    ╔════════════════════════════╦═════════════════════════════════════════════════╗
    ║ No CMD                     ║ exec_entry p1_entry                             ║
    ╟────────────────────────────╫─────────────────────────────────────────────────╢
    ║ CMD ["exec_cmd", "p1_cmd"] ║ exec_entry p1_entry exec_cmd p1_cmd             ║
    ╟────────────────────────────╫─────────────────────────────────────────────────╢
    ║ CMD ["p1_cmd", "p2_cmd"]   ║ exec_entry p1_entry p1_cmd p2_cmd               ║
    ╟────────────────────────────╫─────────────────────────────────────────────────╢
    ║ CMD exec_cmd p1_cmd        ║ exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd  ║
    ╚════════════════════════════╩═════════════════════════════════════════════════╝
 ```   
    
## Useful Docker command:

### listing all the images: 

    docker images
    docker images -a  //for listing local and remote images
 
### listing all the running containers: 
  
    docker ps
    docker ps -a //for listing all the containers running and stopped.
    
### remove image:

    docker rmi -f image:tag //-f is for force remove an image
    
### removing the running/exited containers  

    docker ps -a | grep bootdocker  //find the container
    docker rm bootdocker            //remove the container
    
### get inside the docker container:

    docker exec -it <container_name> /bin/bash      # we execute a interactive terminal (-it) 
                                                    #  on container and start a bash terminal.
                                                    # some systems dont have bash we can use /bin/sh
#### typing `env` inside terminal will print all the environmental parameters

## Docker Volume:

To persist data change made by the application, even after the restart (its lost post restarting a container). Now docker container rns on a HOST, and host would have a persistent system. We can add HOST persistent system to the container. This is a 2-way communication, means any change done by container will persist on HOST, and and change done to HOST file system, will reflect in Container.   
example: if we want to persist logs.

there are 3 types of Volumns:
1. HOST VOLUMN: when we give a host path to be mapped to container path.  
               ![host volumn](/images/hostvolumn.png)
               `-v /home/mount/data :/var/lib/mysql/data`
               
2. ANONYMOUS VOLUMN: When we do not give the host path, it creates anonymous volumns (without any names only hashes) it is found on host system at `var/lib/docker`
3. NAMED VOLUMN: we only give a name to the container path:
                 `-v db-data: /var/lib/mysql/data`  
                 here `db-data` is only a name and is pointing to containers path: `/var/lib/mysql/data`

   
## Troubleshooting:
if you get below error while running a docker container:
    `docker run -d --name bootdocker -p 8080:8080 bootdocker:1`
    
    
    docker: Error response from daemon: Conflict. The container name "/bootdocker" 
    is already in use by container
    "e2abd7fafa24b1572a1d38ef69072d9c623240d81e6e22d3a270f3cb126675e4".
    You have to remove (or rename) that container to be able to reuse that name. See 'docker run --help'.
    

That means there is already a container, either exited or running: remove the container using `docker rm <container-name>`

https://medium.com/geekculture/docker-basics-and-easy-steps-to-dockerize-spring-boot-application-17608a65f657

in order to push to remote you **HAVE** to tag it:

    docker tag <local_image>:<tag>  <usersname>/<image_name>:<tag>
    
    docker tag bootdocker:1 sagarsumit03/bootdocker:1
    docker push sagarsumit03/bootdocker:1

---

# REAL WORLD EXAMPLE:
here we will attach our spring boot application to mongoDB and use mongoExpress to see all the data changes. Here the 2 services mongoDB, mongoExpress will be on docker container.
The mongoDb and mongoExpress can talk to each other with their defined name, as they are on same network.

- Dockerfile is a simile text file contains commands to assemble the image. whereas docker compose defines multiple containers at once. Docker-compose creates a new docker network as well.
- without docker-compose we can deploy/start/run individual containers. Meaning first start the mongoDb container then the mongo-express container.
- first we will build our Spring boot application using below command:  
  `docker build -t bootdocker:1 . ` the `.` is for building in current directory.
  here the `bootdocker` is the image name docker will create and tag it with version `1`:
```
[+] Building 2.4s (8/8) FINISHED                                                                                                                           
   => [internal] load build definition from Dockerfile                                                                                                  0.0s
   => => transferring dockerfile: 297B                                                                                                                  0.0s
   => [internal] load .dockerignore                                                                                                                     0.0s
   => => transferring context: 2B                                                                                                                       0.0s
   => [internal] load metadata for docker.io/library/openjdk:8-jdk-alpine                                                                               2.3s
   => [auth] library/openjdk:pull token for registry-1.docker.io                                                                                        0.0s
   => [internal] load build context                                                                                                                     0.0s
   => => transferring context: 28B                                                                                                                      0.0s
   => [1/2] FROM docker.io/library/openjdk:8-jdk-alpine@sha256:94792824df2df33402f201713f932b58cb9de94a0cd524164a0f2283343547b3                         0.0s
   => CACHED [2/2] COPY target/*.jar app.jar                                                                                                            0.0s
   => exporting to image                                                                                                                                0.0s
   => => exporting layers                                                                                                                               0.0s
   => => writing image sha256:4c6dbc7051e374f1e9696a5f5cc433ef181e610ebec8f08e52eda6c0856f527d                                                          0.0s
   => => naming to docker.io/library/bootdocker:1                                                                                                       0.0s
```
- to check we can run: This can be seen using the `docker images`:
```
docker images | grep bootdocker                                                                              
bootdocker          1                 4c6dbc7051e3   3 days ago      103MB
```
- To run this image we can use: `docker run -d --name bootdocker -p 8080:8080 bootdocker:1 `  
here, we are asking docker to run the image with `container name`(the container will name whatever we define in the name tag) bootdocker and port forward to 8080 and lastly giving the image bootdocker:1.
below is the output:
```
CONTAINER ID   IMAGE          COMMAND                CREATED       STATUS       PORTS                    NAMES
28c97a0a8d85   bootdocker:1   "java -jar /app.jar"   9 hours ago   Up 9 hours   0.0.0.0:8080->8080/tcp   bootdocker
```
- PORT FORWARDING:
  > port forwarding is that the docker container starts at 8080/tcp but it cant be accessed by the outside world, HOTS/LOCAL system.
  > to access anything outside docker we have to forward the internal port to outside port:
  > example: `-p 1234:8080` here the container has started at `8080` but we can access that container using port `1234`. Left is outside. Right is inside.
- now to run mongodb and express in a network, lets create a network:
```
docker network create mongo-network
docker network ls
docker network ls | grep mongo                                                                                
393269c46c2a   mongo-network             bridge    local
```
  
- similarly we can run mongoDb and mongo-express by using below commands:  
  Here we can see that we are running the mongodb in detach mode `(-d)` which will run mongo in background.
  other than that we have passed network as `--net` or `--network`.
  We have some mandatory/overriding environment variable such as username password for the db. we provide them uising  `-e` tag
  and lastly the port forwarding `-p`.
```
docker run -d --network mongo-network --name my-local-mongo -p 27017:27017 \                             
      -e MONGO_INITDB_ROOT_USERNAME=sumit \
      -e MONGO_INITDB_ROOT_PASSWORD=secret \
      mongo
```
- here we are using the the same username password we defined in mongoDB container, and same network as mongodb. lastly the mandatory config: `ME_CONFIG_MONGODB_SERVER` this takes in the name of the mongoDB you want to view. here in our case we had named the mongoDB container as `my-local-mongo` referenced the same.
  
```
docker run -d -p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=sumit \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=secret \
--network mongo-network\
--name my-local-mongo-ui \
-e ME_CONFIG_MONGODB_SERVER=my-local-mongo \
mongo-express
```
  
  
- As we can see that we have to run multiple containers to start the application. Instead of running each container individually we can run it using `docker-compose` this allows to configure everything under a single file:
```
version: "3.9"                                # this is the latest DOCKER-COMPOSE VERSION.
services:                                     # under services you mention all the docker containers you want to run.                         
  my-local-mongo:                             # name of your choice
    image: "mongo"                            # actual image name, can be https://<private-repo>/sagarsumit03/mongo:<tag>
    ports:                                    # ports you want to listen to
      - "27017:27017"
    environment:                              # all the environment variables you want to set using -e tag in docker run
      - MONGO_INITDB_ROOT_USERNAME=sumit
      - MONGO_INITDB_ROOT_PASSWORD=secret
    volumes:                              
      - mongo-data:/data/db                   # this is a named volumn, points to the /data/db. its mongos default path. 
  my-local-mongo-ui: # name of your choice
    image: "mongo-express"
    restart: always # fixes MongoNetworkError when mongodb is not ready when mongo-express starts
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=sumit
      - ME_CONFIG_MONGODB_ADMINPASSWORD=secret
      - ME_CONFIG_MONGODB_SERVER=my-local-mongo
volumes:
  mongo-data:
    driver: local
```

  > `--driver local` means the volumes mongo-data is created on the same Docker host where you run your container. By using other Volume plugins, e.g.,  
    ```
    --driver=flocker
    ```  
    you are able to create a volume on a external host and mount it to the local host, say, /data-path. So, when your container writes to /data-  path, it actually writes to a external disk via network.

 - To bring up the containers using compose use: `docker-compose -f mongo.yaml up` here -f is file.
 - To bring down the containers using compose use: `docker-compose -f mongo.yaml down`. This will stop and remove the mentioned containers and network.
