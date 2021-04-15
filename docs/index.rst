.. Docker FastAPI projects documentation master file, created by
   sphinx-quickstart on Wed Apr 14 16:49:37 2021.
   You can adapt this file completely to your liking, but it should at least
   ontain the root `toctree` directive.

Docker FastAPI projects
=======================

*Well-documented examples of deployment-ready FastAPI applications written from scratch.*

So, you want to include a docker in your FastAPI app, but there is a Great Wall?
--------------------------------------------------------------------------------

**That's definitely a good place to learn how to jump over it.**

Although the official `FastAPI`_  documentation covers many basic topics, for developers unfamiliar with `Docker`_, deploying a real-world backend API can be a nightmare. There is a huge gulf between the hello-world application on ``localhost:8000`` and the application in production.

This project is an attempt at an orderly way through the process of creating more and more complex APIs, with an emphasis on deployment using ``Docker``.

Nice to have:
-------------

* Knowledge of the basics of `REST`_.
* At least basic knowledge of ``Python`` and `FastAPI`_.
* Literally any knowledge of ``Docker`` (`Docker For Beginners Video on YouTube`_, `Dockerfile official reference`_).
* `Docker Desktop client`_ installed on your machine.
* Any experience with cloud platforms like `AWS`_, `Azure`_, `Digital Ocean`_, `Google Cloud`_ etc. (While an ``AWS EC2 instance`` will be used in the project, any virtual machine with docker and ssh access will also be excellent).

.. toctree::
   :maxdepth: 1
   :caption: Basics:

   introduction
   basic_uvicorn

.. _Docker: https://www.docker.com/
.. _REST: https://restfulapi.net
.. _FastAPI: https://fastapi.tiangolo.com
.. _Docker For Beginners Video on YouTube: https://www.youtube.com/watch?v=i7ABlHngi1Q 
.. _Dockerfile official reference: https://docs.docker.com/engine/reference/builder/
.. _Docker Desktop client: https://docs.docker.com/get-docker/
.. _AWS: https://aws.amazon.com/
.. _Azure: https://azure.microsoft.com/
.. _Digital Ocean: https://www.digitalocean.com/
.. _Google Cloud: https://cloud.google.com/

GitHub repository
-----------------

https://github.com/rafsaf/docker-fastapi-projects
