.. title: MySQL
.. slug: mysql
.. date: 2014/05/08 12:49:38
.. tags:MySQL 
.. category: Databases
.. link: 
.. description: 
.. type: text

MySQL tips:
===========

Create database with charset utf8

.. code-block:: sql

   CREATE DATABASE mynew_db_name CHARACTER SET utf8 COLLATE utf8_general_ci;

Grant everything to local user on all tables 

.. code-block:: sql

   GRANT ALL PRIVILEGES ON db_name.* TO my_user@localhost IDENTIFIED BY 'my_secret_password';

Grant everything to remote user on all tables 

.. code-block:: sql

   GRANT ALL PRIVILEGES ON  db_name.* TO my_user@'IP_of_host_user_connects_from' IDENTIFIED BY 'my_secret_password';

If you want to add only **SELECT** or something else instead of **ALL** there are other available privileges like:

**SELECT, UPDATE, ALTER, CREATE, DELETE, DROP, SHOW DATABASES** 
CREATE TEMPORARY TABLES, EXECUTE, FILE, GRANT OPTION, INDEX, INSERT, LOCK TABLES, PROCESS, REFERENCES, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, SHUTDOWN, SUPER, USAGE

After all you want to reload privileges because when mysqld starts, it reads all grant table contents into memory. The in-memory tables become effective for access control at that point. 
To tell the server to reload the grant tables, perform a flush-privileges operation. 

Execute sql :

.. code-block:: sql
   
   FLUSH PRIVILEGES ;

or you can even do it from shell by

.. code-block:: bash

   $ mysqladmin flush-privileges 
   
or 

.. code-block:: bash

   $ mysqladmin reload
