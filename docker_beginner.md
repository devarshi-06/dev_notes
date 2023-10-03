## Container
- One type of box that will come with all the things needed

## Why to do ?
- Because dependencies are varies from computer to computer
- I am using node v16 globally and My project runs on it. My friend used node v18, and try to run my project. It not work as expected.
- That's why we use containers

## How container happens ?
- By running an Image (Image == Blueprints)

## Docker Images

Contains
- Parent Image (OS and sometime runtime enviornment)
- Source Code
- Dependencies
- Extra Config
- envs

==> It contains first layer as parent image (which is os and runtime in some cases)

Images are made of certain layers
- run commands [top]
- dependencies [top-1]
- source code [top-2]
- parent image [top-3]

- To make image write Dockerfile
- Once image is created it is read only, so for every change new image have to build.
## Dockerfile

```
FROM node:17-alpine -> (Get node v17 alpine image) --get from-- (Dockerhub | own compute if exist in local)
COPY . /app -> (copy our folder to root in container)

Problem this command will run in root folder -> but it don't have, so we have to run in app
RUN npm install -> (Run Command) ---Dependencies
```
if COPY . . means copy from this root to that root.
if source code is in src than copy ./src to ,

v2
```
FROM node:17-alpine

WORKDIR /app
-----now all command will work from this folder------
COPY . . -> (Because now we workdir is app)

RUN npm install
EXPOSE 4000 -> (In whcih port container will expose)
CMD [ "node", "app.js" ] -> (Run at runtime) (Image is building app, so don't use RUN node app.js)
```


## How to build image

docker build -t myapp .

breaking down<br/>
-t -> for tagging<br/>
myapp -> tag name<br/>
. -> relative path for Dockerfile directory<br/>


## How to see images ?
```
docker image ls ||
docker images ||
docker image list
```

## DockerIgnore

- same as git, ignore files from source code or folders while building image

```
node_modules
*.md
```

## How to create a container ?

### steps

1. List out images
```
docker images
docker image ls
```

2. Create and run container<br/>
    a. Create a container with name
        ```
        docker run --name myapp_c1 myapp
        ```
    
    b. Create a container name with outside port mapping
    - Note: If your app is listening to port 4000 in app, and your container is exposing in port 4000, if you see localhost:4000 it will not show anything. For that showing we have to specify a port

        ```
        docker run --name myapp_c1 -p 5000:4000 -d  myapp
        ```
    Here -d flag is to detach terminal, otherwise your terminal is blocked and show like server listen type interface.
    Here -p is spacify port. Right -> Docker and left -> our app
    It is port mapping.

3. Now container is running

- Restarting an existing container
```
docker start myapp_c1
```

- You can not run an existing container

- stopping a container
```
docker stop (container_name || conatiner_id)
```

- showing all currently active container
```
docker ps
```

- showing all container
```
docker ps -a
```

## Layer caching

```
FROM node:17-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 4000
CMD [ "node", "app.js" ]
```
In upper code, one line add one layer in image
So, every line is represent adding new layer and that's way image is build.

When we build it first time, docker stored it in cached for every step
- Docker try to find it when creating new image for this, and then use cache

### Edge Case think
- If we change code inside app or source code it gonna affect copy layer, so docker take before layer as cached and copy and after layer gonna be build up.
- Let's think of a new thing
Here, we don't want to re-build npm install, if source code changes. So,we will modify it so npm install takes from caches
```
FROM node:17-alpine
WORKDIR /app
RUN npm install
COPY . .
EXPOSE 4000
CMD [ "node", "app.js" ]
```
But again, if we are building it, it won't find package.json. So, we will do this.
```
FROM node:17-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 4000
CMD [ "node", "app.js" ]
```
So, this is layer caching


## Manages Images and Conatiners

- For removing an image that are not using by container
```docker image rm myapp``` (If any conainre use that image, it gives error)

- Removing an image that use by container
```docker image rm myapp -f```
or
1. Delete a container
```docker container rm app_c2```
I don't know but sure to try out this command using IDs
for removing multiple container
```docker container rm c1 c2 c3```

2. Delete an image as previous command
```docker image rm myapp```
---------------------------------------------------------

```docker system prune -a``` -> remove all images, container and volumes

- To get tag o image
```docker build -t myapp:v1 path_to_dockerfile```
- to run this for container
```docker run --name myapp_c -p 4000:4000 myapp:v1```
----------------------------------------------------------

My sightseeings

- If you build new image with new tag or image with new tag, If something is not changes, it takes steps from caching

## Volumes
- Give a way to map directory to container, so we can see changes , if we change source code things
- map our folder to container folders

```docker run --name myapp_c_nodemon -p 4000:4000 -d --rm myapp:nodemon``` Here, rm flag means remove or deleting container once we stop

remember one thing, that all the flag comes before image name.

We can add volume here
```docker run --name myapp_c_nodemon -p 4000:4000 -d --rm -v local_path:container_path myapp:nodemon```
If anything changes in project, it changes in container too
But for node modules related. Do this to remove node module connection
```docker run --name myapp_c_nodemon -p 4000:4000 -d --rm -v local_path:container_path -v node_modules myapp:nodemon```

## Docker compose

- to handle multiple container for a project
- Don't need to individually manage and run each container

```
version: "3.8"
services:
  api:
    build: ./api
    container_name: api_c
    ports:
      - '4000:4000'
    volumes:
      - ./api:/app
      - /app/node_modules
```

- In docker compose volume is relative path while in termianl it was absolute path
- run ```docker-compose up```, that's it
- ```docker-compose down``` will delete all conatainers

```docker-compose down --rmi all -v``` It will remove images and volumes and containers also.

----------------------------------------------------------
Demo
```
version: "3.8"
services:
  api:
    build: ./api
    container_name: api_c
    ports:
      - '4000:4000'
    volumes:
      - ./api:/app
      - /app/node_modules
  myblog:
    build: ./myblog
    container_name: myblog_c
    ports: 
      - '3000:3000'
    stdin_open: true
    tty: true
```
