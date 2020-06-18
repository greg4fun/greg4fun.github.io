.. title: Build fully working zabbix server with database in seconds thanks to docker
.. slug: build-fully-working-zabbix-server-with-database-in-seconds-thanks-to-docker
.. date: 2018/03/16 17:47:56
.. tags: docker, zabbix, monitoring, docker-compose, DevOps
.. link: 
.. description: 
.. type: text

To install zabbix server quickly zabbix comes with help as they have prebuild their product with docker images.
There is lots of official zabbix images on dockerhub so it can just overwhelm you.
There are mixes of all different possibilities like zabbix with mysql or postgres or either sqlite, 
zabbix served bt nginx or apache or java gateway.
Depending on stack which is closest to you you can easily build docker-compose that will just run selected stack in
seconds. 
My pick was nginx mysql so to set up fully running zabbix server we need 3 images

* mysql-server
* zabbix-web  - web interface
* zabbix-server - main zabbix process responsible for polling and trapping data and sending notifications to users.


In addition you can add postfix mail server for notifying users but its not 
a must as you can use your own mail server if so - just remove postfix service from example below.

Notice(you may want to use some specific versions or alpine versions for production env)

Create some directory (directory name is crucial here for visibility and future maitenance
of your containers and volumes or networks as the name will be used as prefix 
for docker containers created by docker-compose and also volumes directories 
so it will be easier to identify in future which volume belongs to which stack 
In Ubuntu volumes are usually being kept in/var/lib/docker/volumes but you can mount any directory from host by 
just specifying absolute or relative path in service configuration so for instance for mysql in 
example to mount mysql_data_dir just outside of our containers folder 

.. code-block:: bash

    volumes:
      - '../mysql_data_dir:/var/lib/mysql'

Now within directory create docker-compose.yml with selected technologies in my case it is:
#docker-compose.yml

.. code-block:: bash

    version: '3'

    services:
      db:
        image: mysql:latest
        restart: always
        expose:
          - '3336'
        environment:
          MYSQL_ROOT_PASSWORD: 'my_secret_password'
          MYSQL_USER: 'zabbixuser'
          MYSQL_PASSWORD: 'zabbix_password'
          MYSQL_ROOT_HOST: '%'
        volumes:
          - 'mysql_data_dir:/var/lib/mysql'
     

      zabbix-server:
        image: zabbix/zabbix-server-mysql
        links:
          - "db:mysql"
          - "postfix:postfix"
        environment:
          MYSQL_ROOT_PASSWORD: 'my_secret_password'
          MYSQL_USER: 'zabbixuser'
          MYSQL_PASSWORD: 'zabbixpassassword'
          DB_SERVER_HOST: 'mysql'


      zabbix-web:
        image: zabbix/zabbix-web-nginx-mysql
        ports:
          - '7777:80'
        links:
          - "db:mysql"
          - "zabbix-server:zabbix-server"
          - "postfix:postfix"
        environment:
          MYSQL_ROOT_PASSWORD: 'my_secret_password'
          MYSQL_USER: 'zabbixuser'
          MYSQL_PASSWORD: 'zabbixpassassword'
          DB_SERVER_HOST: 'mysql'
          ZBX_SERVER_HOST: "zabbix-server"
          PHP_TZ: "Europe/London"
      postfix:
        image: catatnight/postfix
        hostname: support
        environment:
          - maildomain=mydomain.com
          - smtp_user=admin:my_password
        ports: 
          - "25:25"
        expose:
          - "25"
        volumes:
          - /etc/nginx/ssl/postfix:/etc/postfix/certs
          - /etc/nginx/ssl/postfix:/etc/opendkim/domainkeys
    volumes:
      mysql_data_dir:
        driver: local
     

The above solution is just enough to start zabbix server up and running in couple seconds. 
To do it just run:
.. code-block:: bash

    sudo docker-compose up 


Thats it!!! 
You now have your zabbix running on port 7777

So what happened here docker-compose up has build and runned 3 containers
by running zabbix container it discovered there are no tables in mysql and has built them.

Now you just need to add agents/servers you want to monitor.
Check out adding agent in separate post



Versions:
(versions I've used in this example Feb 2018):

Docker-compose: 1.17.0, build ac53b73
Docker: 17.09.1-ce, build 19e2cf6
Kernel: 4.13.0-36-generic
System: Ubuntu 16.04.3 LTS
