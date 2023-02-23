---
author: Patrik Kaura
pubDatetime: 2023-02-24T20:46:00Z
title: Multistage build and layer caching in Docker
postSlug: multistage-build-and-layer-caching-in-docker
featured: false
draft: false
tags:
  - docker
ogImage: ""
description: Advanced docker techniques.
---

## Multistage build

A multistage build is a technique that `separates` the build process
from the production version of an application.
The first stage is `responsible for building the application` and
contains files such as the package.json file.
The second stage is used for `production` and only `copies the 
necessary files from the first stage`.

```dockerfile
# This stage creates production build

FROM node:10.15.2-alpine AS appbuild
WORKDIR /usr/src/app
COPY package.json ./
COPY .babelrc ./
RUN npm install
COPY ./src ./src
RUN npm run build

# Copy built application and run

FROM node:10.15.2-alpine
WORKDIR /usr/src/app
COPY --from=appbuild /build ./public
EXPOSE 4002
CMD npm start
```

## Docker layer caching

Layer caching is a `mechanism used during the build process` of a
Docker image, where each build consists of `multiple layers`.
Each command in the Docker build file `represents a layer`.
It is generally better to place the commands that are `least 
likely to change first` in the Docker build file to take advantage
of caching. If, for example, source files are modified, the copy
command is not taken from the cache, and all subsequent commands
are invalidated and rerun. The picture below shows all the layers
of the official Node.js image.

![caching](/assets/docker/caching.png)
