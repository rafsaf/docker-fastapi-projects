Deployment in AWS EC2 instance
==============================

Each of the projects can be easily implemented on the AWS EC2 instance. Since there are tons of articles on this subject, below we will only focus on the stage where you already have access to the console on the instance.

.. admonition:: You should probably follow official documentation  

  | First steps with EC2
  | https://docs.aws.amazon.com/AmazonECS/latest/developerguide/get-set-up-for-amazon-ecs.html

  | Docker basics
  | https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html



Remember that your inbound rules should appear like this:

Inbound rules
-------------

+------+----------+------------+--------------+
| Type | Protocol | Port range | Source       |
+======+==========+============+==============+
| HTTP | TCP      | 80         |  0.0.0.0/0   |
+------+----------+------------+--------------+
| HTTP | TCP      | 80         |  ::/0        |
+------+----------+------------+--------------+
| SSH  | TCP      | 22         | YOUR_HOME_IP |
+------+----------+------------+--------------+

With Docker and Git installed on your instance
----------------------------------------------

Going live is as simple as:

.. code-block:: bash
  
  git clone https://github.come/some-user/some-repository.git

  cd some-repository

  docker build . -t my_tag_name

  docker run -t -i -p 80:80 my_tag_name:latest

Now by typing ``my_instance_IP`` in the browser you will see the server replies.


