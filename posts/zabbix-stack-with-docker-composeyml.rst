.. title: Zabbix stack with docker-compose.yml
.. slug: zabbix-stack-with-docker-composeyml
.. date: 2018/03/15 17:14:48
.. tags: draft
.. link: 
.. description: 
.. type: text

Fully working zabbix server solution with UI and database in seconds
====================================================================

I wanted to install zabbix server quickly with docker but amount of zabbix images (created by zabbix) on dockerhub just overwhelmed me.
To set up running zabbix server we need 3 images 
* choice of sql DB 
* zabbix-web  - web interface
* zabbix-server - main zabbix process responsible for polling and trapping data and sending notifications to users.


My choice of database was MySQL so I created docker-compose file to have full stack of running zabbix server:

Notice(you may want to use alpine versions for production env)
docker-compose.yml:


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
          MYSQL_PASSWORD: 'zabbixpass'
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
          MYSQL_PASSWORD: 'zabbixpass'
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
          MYSQL_ROOT_PASSWORD: 'secret'
          MYSQL_USER: 'zabbixuser'
          MYSQL_PASSWORD: 'myzabbixpass'
          DB_SERVER_HOST: 'mysql'
          ZBX_SERVER_HOST: "zabbix-server"
          PHP_TZ: "Europe/London"
      postfix:
        image: catatnight/postfix
        hostname: support
        environment:
          - maildomain=domain.com
          - smtp_user=admin:password
        ports: 
          - "25:25"
        #  - "465:465"
        #  - "587:587"
        expose:
          - "25"
        #  - "465"
        #  - "587"
        volumes:
          - /etc/nginx/ssl/postfix:/etc/postfix/certs
          - /etc/nginx/ssl/postfix:/etc/opendkim/domainkeys
    volumes:
      mysql_data_dir:
        driver: local
     
          #- ./deployment/config_files/main-postfix-live.cf:/etc/postfix/main.cf
        #networks:
        #  - backend
        #entrypoint: /docker-entrypoint.sh
     


The above solution is just enough to start zabbix server up and 
running in couple seconds. To run it just put yml file into some directory (directory is important as volume created for mysql will have this dir name as prefix) volumes are usually stored in /var/lib/docker/volumes and run:

.. code-block:: bash

    sudo docker-compose up 


Thats it!!! 
You now have your zabbix running on port 7777

So what happened here docker-compose up has build and runned 3 containers
by running zabbix container it discovered there are no tables in mysql and has built them.

Now you just need to add agents/servers you want to monitor.
Check out adding agent in separate post [here]



Versions:
(versions I've used in this example Feb 2018):

Docker-compose: 1.17.0, build ac53b73
Docker: 17.09.1-ce, build 19e2cf6
Kernel: 4.13.0-36-generic

