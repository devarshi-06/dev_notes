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

breaking down
-t -> for tagging
myapp -> tag name
. -> relative path for Dockerfile directory