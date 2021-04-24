# Docker FastAPI projects

_Well-documented examples of deployment-ready FastAPI applications written from scratch._

https://docker-fastapi-projects.readthedocs.io/en/latest/

## So, you want to include a docker in your FastAPI app, but there is a Great Wall?

**That's definitely a good place to learn how to jump over it.**

Although the official `FastAPI` documentation covers many basic topics, for developers unfamiliar with `Docker`\_, deploying a real-world backend API can be a nightmare. There is a huge gulf between the hello-world application on `localhost:8000` and the application in production.

This project is an attempt at an orderly way through the process of creating more and more complex APIs, with an emphasis on deployment using `Docker`.

## Nice to have:

- Knowledge of the basics of `REST`.
- At least basic knowledge of `Python` and `FastAPI`.
- Literally any knowledge of `Docker` (`Docker For Beginners Video on YouTube`, `Dockerfile official reference`).
- `Docker Desktop client` installed on your machine.
- Any experience with cloud platforms like `AWS`, `Azure`, `Digital Ocean`, `Google Cloud` etc. (While an `AWS EC2 instance` will be used in the project, any virtual machine with docker and ssh access will also be excellent).

# Uvicorn

This dead simple application shows you to how to create a simple Docker Image with running FastAPI app with Uvicorn and presenting the basics of creating and running Docker Images.

[See chapter dedicated to this repository](https://docker-fastapi-projects.readthedocs.io/en/latest/uvicorn.html)

# Nginx

This dead simple application shows you to how to create a simple Docker Image with running FastAPI app with Nginx and presenting the basics of creating and running Docker Images.

[See chapter dedicated to this repository](https://docker-fastapi-projects.readthedocs.io/en/latest/nginx.html)

# Nginx with PostgreSQL

This dead simple application shows you to how to create a simple Docker Image with running FastAPI app with Nginx and presenting the basics of creating and running Docker Images.

[See chapter dedicated to this repository](https://docker-fastapi-projects.readthedocs.io/en/latest/nginx_with_postgresql.html)

You should have `.env` file in root of the project to run it.
Template for `.env` can be found in `.example.env`.
