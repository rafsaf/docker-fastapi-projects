.. admonition:: Github Repo for this application

  https://github.com/rafsaf/docker-fastapi-projects-uvicorn

Uvicorn
=======


This dead simple application shows you to how to create a simple Docker Image with running FastAPI app with Uvicorn and presenting the basics of creating and running Docker Images. Each step is deeply described below.


Application structure
---------------------

.. code-block:: bash

  ├── app
  |   |── main.py           # FastAPI simple app
  |
  ├── .dockerignore         # similar to .gitignore
  |                         # but for Docker
  ├── .gitignore
  |
  ├── Dockerfile            # commands to build
  |                         # a Docker Image
  ├── README.md
  |
  ├── requirements.txt      # pip freeze from
  |                         # pip install fastapi uvicorn

Files description
-----------------


**requirements.txt**, **.gitignore**, **.dockerignore**

  Some common stuff. In ``.dockerignore`` I've added ``README.md`` - we don't need it in our Container.

**app/main.py**

  .. code-block:: python

    from fastapi import FastAPI

    app = FastAPI()


    @app.get("/")
    async def root():
        return {"Uvicorn": "I'm alive"}

**Dockerfile**

  .. code-block:: dockerfile

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


Local Deployment
----------------

.. code-block:: bash
  :linenos:

  git clone https://github.com/rafsaf/docker-fastapi-projects-uvicorn.git

  cd docker-fastapi-projects-uvicorn

  docker build . -t uvicorn

  # creates image in current folder with tag uvicorn

  docker run --rm -it  -p 80:80/tcp uvicorn:latest

  # runs uvicorn image

Now the app is up and running locally. In your favourite browser type in:

``localhost``

You should see:

.. code-block:: bash
  :linenos:

  {"Uvicorn": "I'm alive"}

Awesome!


Troubleshoots
-------------

Host 0.0.0.0
************


Wasn't it just ``uvicorn main:app --reload`` in tutorial? I understand ``--port 80``, but why do we need ``--host 0.0.0.0``? What's a diffrence?

  The diffrence is crucial! You need bind a server to ``0.0.0.0`` so traffic coming from outside of the container is also accepted. If you don't, it will not be reachable from outside the container no matter what you do.


CMD command
***********

Ok, but why on earth do we need to type in ``CMD python -m uvicorn main:app --host 0.0.0.0 --port 80`` in our ``Dockerfile`` instead of ``unicorn (...)``?

  Why not? Sure, we can also use ``uvicorn main:app --host 0.0.0.0 --port 80``

Container port
**************

How to map say port ``8888`` of the Container to our ``localhost:80``?

  Just change ``EXPOSE 80`` to ``EXPOSE 8888`` for a Container itself and also ``--port 80`` to ``--port 8888`` for ``uvicorn`` server inside.
  Then use ``docker run --rm -it  -p 80:8888/tcp first_project:latest``

Local port
**********

OK i have my favourite port ``8888`` but I don't like ``localhost:80`` in my browser, i would love to use ``localhost:3001`` instead.

  Just map to a diffrent port ``docker run --rm -it  -p 3001:8888/tcp first_project:latest``