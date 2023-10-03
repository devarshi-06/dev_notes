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