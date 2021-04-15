.. admonition:: Github Repo for this application

  https://github.com/rafsaf/docker-fastapi-projects-nginx

.. note:: Useful links

  https://unit.nginx.org/howto/fastapi/

.. role:: green

Nginx
=====


This dead simple application shows you to how to create a simple Docker Image with running FastAPI app with Nginx and presenting the basics of creating and running Docker Images. Each step is deeply described below.
You should definitely have :class:`any` clue about what's going on here, there is a great video which helped me out: `Docker For Beginners Video on YouTube`_.

.. _Docker For Beginners Video on YouTube: https://www.youtube.com/watch?v=i7ABlHngi1Q 

Application structure
---------------------

.. code-block:: bash

  ├── app
  |    |── app                   
  |    |    ├── __init__.py
  |    |    ├── main.py         # FastAPI app here
  |    |
  |    ├── config       
  |         ├── config.json     # Nginx config file
  |
  ├── .dockerignore             # similar to .gitignore
  |                             # but for Docker
  ├── .gitignore
  |
  ├── Dockerfile                # commands to build
  |                             # a Docker Image
  ├── README.md
  |
  ├── requirements.txt          # pip freeze from
  |                             # pip install fastapi

Files description
-----------------


**requirements.txt**, **.gitignore**, **.dockerignore**

  Some common stuff. In ``.dockerignore`` I've added ``README.md`` - we don't need it in our Container.

**app/app/main.py**

  .. code-block:: python

    from fastapi import FastAPI

    my_fastapi_app = FastAPI()


    @my_fastapi_app.get("/")
    async def root():
        return {"Nginx": "I'm alive"}

**app/config/config.json**

  .. code-block:: json

    {
        "listeners": {
            "*:80": {
                "pass": "applications/fastapi"
            }
        },

        "applications": {
            "fastapi": {
                "type": "python 3.9",
                "path": "/build/app/",
                "module": "app.main",
                "callable": "my_fastapi_app"
            }
        }
    }

  This is a configuration file for our Nginx server.
  Some basic listener :code:`*:80`. If we change **applications** to **apps** and **fastapi** to **python_app** we would have ``"pass": "apps/python_app"``.

  :green:`"type"` ``python 3.9`` is obvious because we're using Docker Image with python version 3.9. Note that we want later in ``Dockerfile`` to copy our repo to ``/build`` folder inside of the Container so :green:`"path"` ``/build/app/`` will be a root folder for our FastAPI application. Then :green:`"module"` is just a python file with application which is ``main.py`` so :green:`"module"` is ``app.main`` and since we named the instance of ``FastAPI`` class in ``main.py`` as a ``my_fastapi_app``, :green:`"callable"` is ``my_fastapi_app``.

  If we had a different folder structure, e.g. one more ``app`` folder and then the ``main.py`` file, then the :green:`"module"` would be ``"app.app.main"``.

  .. note::

      Configuration docs for python apps can be found here:
      https://unit.nginx.org/configuration/#configuration-python

**Dockerfile**

  .. code-block:: dockerfile

    FROM nginx/unit:1.23.0-python3.9

    # Our Debian with Python and Nginx for python apps.
    # See https://hub.docker.com/r/nginx/unit/

    COPY ./app/config/config.json /docker-entrypoint.d/config.json

    # Ok, this is something we get thanks to the Nginx Unit Image.
    # We don't need to call stuff like
    # curl -X PUT --data-binary @config.json --unix-socket \
    #       /path/to/control.unit.sock http://localhost/config/
    # to set our configuration
    # Becouse as stated in docs https://unit.nginx.org/installation/#docker-images,
    # configuration snippets are 
    # uploaded as to the config section of Unit’s configuration
    # That means we only have to copy our config.json file to the folder
    # /docker-entrypoint.d/

    RUN mkdir build

    # We create folder named build for our app.

    COPY . ./build

    # We copy our app folder to the /build

    RUN apt update && apt install -y python3-pip                                  \
        && pip3 install -r /build/requirements.txt                               \
        && apt remove -y python3-pip                                              \
        && apt autoremove --purge -y                                              \
        && rm -rf /var/lib/apt/lists/* /etc/apt/sources.list.d/*.list

    # OK, that looks strange but here's a explanation from Nginx docs
    # https://unit.nginx.org/howto/docker/:

    # """ PIP isn't installed by default, so we install it first.
    # Next, we install the requirements, remove PIP, and perform image cleanup. """

    # Note we use /build/requirements.txt since this is our file

    EXPOSE 80

    # Instruction informs Docker that the container listens on port 80


Local deployment
----------------

In your favourite folder:

.. code-block:: bash
  :linenos:

  git clone https://github.com/rafsaf/docker-fastapi-projects-nginx.git

  cd docker-fastapi-projects-nginx

  docker build . -t nginx

  # creates image in current folder with tag nginx

  docker run --rm -it  -p 80:80/tcp nginx:latest

  # runs nginx image

Now the app is up and running locally. In your favourite browser type in:

``localhost``

You should see:

.. code-block:: bash
  :linenos:

  {"Nginx": "I'm alive"}

Awesome!

