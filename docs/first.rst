First
=====

Motivation: Without knowing the Docker, it was really painfull to get started with FastAPI to build production-ready application running in a Docker Container.

The best video source that I found about Docker for begginers: [Docker For Begginers: From Docker Desktop to Deployment](https://www.youtube.com/watch?v=i7ABlHngi1Q&t=2065s).

This repository allows you to create a simple Docker image with running FastAPI on localhost:80 presenting the basics of creating and running Docker Images. Each step is deeply described below.

## Table of contents

- [Project structure](#project-structure)
- [Files description](#files-description)
- [Get started](#get-started)
- [Troubleshoots](#troubleshoots)

# First project

## Project structure

```
.
├── app
|   |── main.py       # FastAPI simple app
|
├── .dockerignore     # similar to .gitignore
|                     # but for Docker
├── .gitignore
|
├── Dockerfile        # commands to build
|                     # a Docker Image
├── README.md
|
├── requirements.txt  # pip freeze from
|                     # pip install fastapi[all]
```

## Files description

`requirements.txt`, `.gitignore`, `.dockerignore `

Some common stuff. In `.dockerignore` I've added `venv/ ` and `.vscode` - we don't need it in our Container.

`main.py`

Basic endpoint from FastAPI tutorial.

`Dockerfile`

```dockerfile
# A Dockerfile is a text document that contains all the commands
# a user could call on the command line to assemble an image.

FROM python:3.9.4-buster
# Our Debian with python is now installed.
# Imagine we have folders /sys, /tmp, /bin etc. there
# like we would install this system on our laptop.

RUN mkdir build
# We create folder named build for our stuff.

WORKDIR /build
# Basic WORKDIR is just /
# Now we just want to our WORKDIR to be /build

COPY . .
# FROM [path to files from the folder we run docker run]
# TO [current WORKDIR]
# We copy our files (files from .dockerignore are ignored)
# to the WORKDIR

RUN pip install --no-cache-dir -r requirements.txt

# OK, now we pip install our requirements

EXPOSE 80

# Instruction informs Docker that the container listens on port 80

WORKDIR /build/app

# Now we just want to our WORKDIR to be /build/app for simplicity
# We could skip this part and then type
# python -m uvicorn main.app:app ... below

CMD python -m uvicorn main:app --host 0.0.0.0 --port 80

# This command runs our uvicorn server
# See Troubleshoots to understand why we need to type in --host 0.0.0.0 and --port 80
```

## Get started

In your favourite folder:

```
$ git clone https://github.com/rafsaf/docker-fastapi-tutorial-first-project.git

$ cd docker-fastapi-tutorial-first-project
```

Then create docker image

```
$ docker build . -t first_project

# creates image in current folder with tag first_project
```

<img src="https://raw.githubusercontent.com/rafsaf/docker-fastapi-projects/main/images/first_project/first_project_1.png" width=600 />

Then you can type out line below or just use great docker client or VS Code with extension for Docker.

```
$ docker run --rm -it  -p 80:80/tcp first_project:latest

# Runs first_project image
```

<img src="https://raw.githubusercontent.com/rafsaf/docker-fastapi-projects/main/images/first_project/first_project_2.png" />

Now your app is up and running locally. In your favourite browser type in:

```
localhost:80
# or just localhost since 80 is a default port
```

You should see:

```
{"message":"Hello World"}
```

## Troubleshoots

### Host 0.0.0.0

Wasn't it just `python -m uvicorn main:app --reload` in tutorial? I understand `--port 80`, by why do we specify `--host 0.0.0.0` ? What's a diffrence?

- The diffrence is crucial! You need bind a server to `0.0.0.0` so traffic coming from outside of the container is also accepted. If you don't, it will not be reachable from outside the container no matter what you do.

### CMD command

Ok but why on earth do we need to type in `CMD python -m uvicorn main:app --host 0.0.0.0 --port 80 ` in our `Dockerfile` instead of ` unicorn etc.`?

- Why not? Sure, we can also type just `uvicorn main:app --host 0.0.0.0 --port 80`

### Container port

What if we want, say map port `8888` or `5100` of the Container to our `localhost:80`?

- No problem, just change `EXPOSE 80` to `EXPOSE 8888` or `EXPOSE 5100` and in ` CMD python` change `--port 80` to `--port 8888` or `--port 5100` and run it like

  ```
  docker run --rm -it  -p 80:8888/tcp first_project:latest

  docker run --rm -it  -p 80:5100/tcp first_project:latest
  ```

### Local port

OK i have my favourite port `8888` but I don't like `localhost:80` in my browser, i would love to use `localhost:3001`.

- Just map to a diffrent port

  ```
  docker run --rm -it  -p 3001:8888/tcp first_project:latest