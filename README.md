## Nilanchal
Taken from - https://github.com/ashleydavis/docker-nodejs-examples/tree/master

$ wsl docker build -t my-nodeapp .
$ docker run -d -it –rm -p [host_port]:[container_port] –name=[container_name] [image_id/image_tag]

		wsl docker run -it -p 3003:3000 --name=mynodeapp my-nodeapp:latest
		docker run -it -p 3003:3000 --name=mynodeapp nexrpa/node-app:latest
		
# Simple Node.js + Docker example

The simplest possible example of using Node.js and Docker.

## Files

- simple/
  - src/index.js      Example Express web server.
  - .dockerignore     Make Docker ignore specified files.
  - Dockerfile        Script file for building our Docker image.
  - package.json      Node.js package file, specifies npm dependencies.

## Setup

You need Node.js and Docker installed.

First change to the directory and install dependencies:

```bash
cd simple
npm install
```

## Directly run

The example Node.js application is just "Hello world", you run it directly like this:

```bash
npm start
```

## Build and run using Docker

To build the Docker image:

```bash
npm run docker:build
```

To run the Docker image:

```bash
npm run docker:run
```

To see what Docker images you have locally:

```bash
npm run docker:ls
```

To see what Docker containers you have running:

```bash
npm run docker:ps
```

To stop the container:

```bash
npm run docker:stop
```