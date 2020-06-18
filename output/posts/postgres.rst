.:w
. title: Postgres
.. slug: postgres
.. date: 2014/05/08 10:20:29
.. tags: 
.. category: Databases
.. link: 
.. description: 
.. type: text

Quick tip:
==========

I started with MySQL db (not speaking of Delphi ADO or something like this )
Now because of my job I had to switch to Postgres and I thought 
"ok but it's SQL so it will be the same" - not really I had a lot of 
problems like in the beginning like user privileges owners of databases and tables, showing tables , showing databases
So if youre switchng from mysql it can help you for beginning:

So first problem was my user access - I couldnt access postgres even through root :)
so after clean installation if you want to launch psql client for postgres just su as postgres after that you can create
your own super user or user having privileges to specific database

.. code-block:: bash
   
   $ su postgres
   $ psql

and then you should be able to do everything 

To see databases "**show databases;**" didn't work anymore :) in postgres

.. code-block:: psql
   
   \list
   \l


connect to database:

.. code-block:: psql
   
   \c db_name

**show tables;** in database:

.. code-block:: psql
   
   \dt

**Explain** or **describe** table:

.. code-block:: psql
   
   \d+ table_name

Show all shortcuts:

.. code-block:: psql
   
   \?

======
Backup
======


To dump database to sql script with inserts etc use pg_dump remember (it wont dump user privileges):

.. code-block:: psql

   pg_dump my_database > my_database.pgdump

The better way of dumping database is pg_dumpall it dumps database users and groups, tablespaces, access permissions that apply to databases as a whole.

.. code-block:: psql

   pg_dumpall > my_dump.pgdump

If you want to move your users of postgres to different server you can:

.. code-block:: psql

   pg_dumpall --roles-only > my_users.pgdump

=======
Restore
=======

TO simply restore backup pgdump sql file :

.. code-block:: psql

   psql < my_database.pgdump


==================
Access Privileges:
==================
Another way is about accessing postgresql if you worked with mysql you could just provide : grant all privileges on db to user_name@localhost or user_name@'10.0.0.1' here it's not the same. Privileges can be configured in low level pg_hba.conf file where you provide who(sys user) who(db_user) where_from . Adding user in postgres:

Create user:

.. code-block:: psql

   create user my_name with password 'secret';

change owner of db:

.. code-block:: psql

   alter database mydatabase  owner to new_owner_name;

change owner of tables:

.. code-block:: psql

   REASSIGN OWNED BY old_role TO new_role

rename db

.. code-block:: psql

   alter  database production rename to production_delete;

