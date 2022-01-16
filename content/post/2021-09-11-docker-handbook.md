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

Let's first look at a very simple dockerfile: 

```
# set base image (host OS)
FROM python:3.8

# copy the content of the local src directory to the working directory
WORKDIR /usr/local/
COPY . .

# install dependencies
RUN apt-get update
RUN pip install -r requirements.txt

# command to run on container start
ENTRYPOINT ["python","hello.py"]
```

The `DockerFile` is a way to tell docker that: '*hey Docker, these are the things that you need to be able to build a docker image of whatever I'm working on.*'. Think of it like a recipe, where the final dish to be prepared is the docker image. 

`COPY . .` tells docker to copy everything in the current directory into the docker image which is being built. In ordr to avoid copying everything, you can use a `.dockerignore` file like the one shown below: 

```
# Ignore any files or directories within the subdirectory named "modules" 
some_folder/*

# Ignore any files or directories in the context root beginning with "modules"
some_name*
```

I personally like to move all of the dependency installations into a different shell script called `setup.sh` and just run it in the dockerfile as follows:

```
RUN sh setup.sh
```

where `setup.sh` could be something like: 

```
pip install --upgrade pip
pip install git+https://github.com/username/repo.git
echo 'setup complete :)'
```

## Building an image

Once you're ready with your dockerfile, it's now time to build the image. This generally takes quite some time if you have tons of dependencies. 

```
nvidia-docker build . --file Dockerfile --tag spongebob
```

* `--file`: specifies the name of the dockerfile, defaults to `Dockerfile`
* `--tag` think of it like the name of the image you're building. In this case I named it `spongebob`

**note:**: `nvidia-docker` and `docker` are the same thing, except for the fact that `nvidia-docker` required when you want to access Nvidia GPUs later on when running the image. 

Once your image has been built, you can check it out with: 

```
docker image ls
```

## Pushing an image into a registry

In order to push your image, you would first have to tag it

```
docker tag spongebob mayukhdeb/spongebob:latest
```

then you can push it into dockerhub as follows: 

```
docker push mayukhdeb/spongebob:latest
```

## Running an image locally

```
docker run mayukhdeb/spongebob:latest
```

In order to open up ports, you'll have to use the `-p` and the `--network` args as follows: 

```
docker run -p 5656:5656 --network="host" spongebob
```

And to use nvidia GPUs, you have to use the `--gpus` arg: 

```
nvidia-docker run  --gpus all -p 5656:5656 --network="host" spongebob
```

As an extra, if you want to also pass CLI args into your script, you can simply append them into the end of the command. Let us try to pass a `--friend` argument into the script:

```
nvidia-docker run  --gpus all -p 5656:5656 --network="host" spongebob --friend="patrick"
```