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
docker image ls

## DockerIgnore

- same as git, ignore files from source code or folders while building image

```
node_modules
*.md
```

## How to create a container ?

###steps

1. List out images
```
docker images
docker image ls
```

2. Create and run container
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