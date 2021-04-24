.. admonition:: Github Repo for this application

  https://github.com/rafsaf/docker-fastapi-projects-nginx-with-postgresql


.. role:: green

Nginx with PostgreSQL
=====================

This application is the basic template consisting of the Nginx webserver and the PostgreSQL database from which most applications can be built, it ensures safe maintenance of user accounts, authorization, and its structure allows you to add new endpoints neatly.

Application structure
---------------------

.. code-block:: bash

  |──.github                                   # github actions
  |
  |── app                   
  |    ├── api                                 # endpoints/dependecies
  |    |    |── routers
  |    |    |      |── __init__.py 
  |    |    |      |── login.py 
  |    |    |      |── users.py 
  |    |    |      
  |    |    |── __init__.py
  |    |    |── api.py
  |    |    |── deps.py
  |    |    | 
  |    |    | 
  |    ├── core                                # settings and security algorithms
  |    |    |── __init__.py           
  |    |    |── config.py           
  |    |    |── security.py           
  |    |               
  |    ├── crud                                # CRUD operations
  |    |    |── __init__.py           
  |    |    |── base.py           
  |    |    |── crud_user.py           
  |    |               
  |    ├── migrations                          # for aerich migrations
  |    |    |── models              
  |    |    |      |── 0_202104..._None.sql             
  |    |                
  |    ├── models                              # Tortoise models
  |    |    |── __init__.py              
  |    |    |── user.py              
  |    |                  
  |    ├── schemas                             # Pandatic schemas
  |    |    |── __init__.py              
  |    |    |── token.py              
  |    |    |── user.py              
  |    |                  
  |    ├── tests                               # tests
  |    |    |── api  
  |    |    |     |── __init__.py         
  |    |    |     |── test_login.py         
  |    |    |     |── test_users.py         
  |    |    |              
  |    |    |── crud 
  |    |    |     |── __init__.py          
  |    |    |     |── test_user.py          
  |    |    |              
  |    |    |── utils  
  |    |    |     |── __init__.py         
  |    |    |     |── user.py         
  |    |    |     |── utils.py         
  |    |    |              
  |    |    |── __init__.py           
  |    |    |── conftest.py           
  |    |               
  |    ├── __init__.py                         
  |    ├── initial.sh                          # initial shell script used by docker
  |    ├── initial_data.py                     # init database and add first superuser
  |    ├── main.py                             # main fastapi application file
  |   
  ├── config       
  |    ├── config.json                         # Nginx config file
  |
  ├── .dockerignore             
  |                             
  ├── .example.env                             # Example for required .env file
  |                             
  ├── .gitignore
  |
  ├── Dockerfile                               # Dockerfile for web app
  |                             
  ├── README.md
  |                             
  ├── aerich.ini                               # Aerich (migrations) configuration
  |                             
  ├── docker-compose.yml                       # puts it all together
  |                             
  ├── pytest.ini                               # Pytest configurations
  |
  ├── requirements.txt                         # python dependencies
  |                             


Files description
-----------------

.. note:: 
  Describing each file in this project seems pointless, so we will only focus on the most important ideas (mainly the ``docker-compose.yml`` file), and you will probably benefit more by reading how to add a new enpoint step by step (from creating a table in the database to migrating, data schema, endpoint and tests)

**docker-compose.yml**

  This file is responsible for creating web server docker containers and postgresql database. You can read more about the syntax in the docker-compose documentation

.. code-block:: yml

  version: "3.9"
  
  services:
    db:
      image: postgres
      volumes:
        - ./data/db:/var/lib/postgresql/data
      ports:
        - "5432:5432"
      environment:
        - POSTGRES_DB=${POSTGRES_DB}
        - POSTGRES_USER=${POSTGRES_USER}
        - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      env_file:
        - .env
    web:
      restart: always
      build:
        context: ./
        dockerfile: Dockerfile
      ports:
        - "80:80"
      depends_on:
        - db
  
**Dockerfile**
  
.. code-block:: bash
    
  FROM nginx/unit:1.23.0-python3.9
  
  # Our Debian with Python and Nginx for python apps.
  # See https://hub.docker.com/r/nginx/unit/
  ENV PYTHONUNBUFFERED 1
  
  COPY ./app/initial.sh /docker-entrypoint.d/initial.sh
  COPY ./config/config.json /docker-entrypoint.d/config.json
  
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
  WORKDIR /build
  
  COPY ./app ./app
  COPY ./pytest.ini .
  COPY ./aerich.ini .
  COPY ./requirements.txt .
  COPY ./.env .
  
  # We copy our app folder to the /build
  
  RUN apt update && apt install -y python3-pip                                  \
      && pip3 install -r requirements.txt                                       \
      && apt remove -y python3-pip                                              \
      && apt autoremove --purge -y                                              \
      && rm -rf /var/lib/apt/lists/* /etc/apt/sources.list.d/*.list
  
  # OK, that looks strange but here's a explanation from Nginx docs
  # https://unit.nginx.org/howto/docker/:
  
  # """ PIP isn't installed by default, so we install it first.
  # Next, we install the requirements, remove PIP, and perform image cleanup. """
  
  # Note we use /build/requirements.txt since this is our file
