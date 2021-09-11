+++


title = "Docker Handbook"
description = "Not very useful to outsiders unless you're looking for overly specific stuff"
date = "2021-09-11"
tags = ["docker"]
slug = "docker-handbook"

summary = "Docker for people who don't dont really want to spend too much time on docker"

+++

Docker is a way for the DevOps engineers to transfer a part of their pain to the devs. 

> In this handbook, you might see me using both `docker` and `nvidia-docker`, the latter is used for container which require a GPU to run. In most of the cases, these are interchangable, except for when you're running containers which require gpus, where `nvidia-docker` is mandatory.

There are 5 steps to moving from your ragtag scripts to docker: 

0. Stop running your ragtag scripts 
1. Write a `Dockerfile`
2. Run `docker build` to build an image
3. (Optional) Push your container into a registry
4. Running your image locally with `docker run`

## Writing a `Dockerfile`

## Building an image

## Pushing an image into a registry

## Running an image locally