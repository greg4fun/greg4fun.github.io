.. title: Adding zabbix agent to server
.. slug: adding-zabbix-agent-to-server
.. date: 2018/03/16 09:27:50
.. tags: docker, zabbix, monitoring, docker-compose, DevOps
.. link: 
.. description: 
.. type: text

Zabbix is very powerfull tool which its using agents (or SNMP) to monitor server resources.
Adding agent is easy but I had couple problems doing that when I used agent straight from my
ubuntus (16.04.3) repo as there was no encryption functionality in this agent well I guess so as 
agent didn't recognize tls psk configuration so not very nice as by installing agent straight form repo 
with "sudo apt-get update && sudo apt-get install zabbix-agent" I had limited functionality and unencrypted server-agent traffic.
So there are 2 options we can install zabbix agent or use zabbix agent docker container.
Adding zabbix agent to host system.
For current day 3.2 is the latest so please change latest accordingly of how this artcile is old.
wget http://repo.zabbix.com/zabbix/3.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.2-1+xenial_all.deb
sudo dpkg -i zabbix-release_3.2-1+xenial_all.deb
sudo apt-get update
apt-get purge zabbix-agent #remove previous if installed
apt-get install zabbix-agent


Now there are 3 basic options that need to be changed in agent config file:
/etc/zabbix/zabbix_agentd.conf

Server=ip of zabbix server
ServerActive=ip of zabbix server
Hostname=My host name

sudo service zabbix-agent restart

Add host to server through web interface:
-----------------------------------------
In server go to Configuration-> Hosts -> Create host
type in host name
visible name
public IP address opf your agent.Select group and add agent.
Next is to select templates so add services you need to monitor (here linux + mysql : Template DB MySQL, Template OS Linux)
after saving you should see green ZBX available label on Hosts screen
Notice : I couldnt see zbx agent green icon until I added linux template / or zabbix agent template.

Seurity - Setting up PSK encryption:
---------------------------------------
sh -c "openssl rand -hex 32 > /etc/zabbix/zabbix_agentd.psk"
Now add below lines to /etc/zabbix/zabbix_agentd.conf
TLSConnect=psk
TLSAccept=psk
#each identity id must be different for each serverr connected to one zabbix server
TLSPSKIdentity=PSK SOMETHING 
TLSPSKFile=/etc/zabbix/zabbix_agentd.psk
sudo service zabbix-agent restart
Get generated key string:
cat /etc/zabbix/zabbix_agentd.psk
and add encryption in zabbix server web interface :
In server go to Configuration-> Hosts -> my host->encryption

Select:
Connections to host PSK
connections from host PSK
PSK identity: PSK SOMETHING (same as in zabbixagent config file)
PSK: the hash generated (content of /etc/zabbix/zabbix_agentd.psk file on agent )
now there should be greebn psk lablel and all our traffice will be encrypted




Adding mysql monitoring option:
-------------------------------

add user credentials for mysqlclient on agent server:
mysql > grant all privileges on *.* to zabbix@'%' identified by 'zabbixuserpassword';
use localhost or host you will be accessing mysql from % is just for test purpose to eliminate 
authentication problems.

        Out of the topic - something about mysql remote connections and security:
        My best practice is not to have any remote access like @'%' to mysql on any server I manage 
        its just dangerous and anyone can try bruteforcing and try to connect to our mysql server. 
        Another way I saw in many places if admin creates @'%' accesses they use it without any encryption so there is
        plain text traffic comming from mysql-server/postgres straight to users computer which is not good (MITM etc).
        The best would be to have your mysql server set up with ssl certificate but its not popular practice as may be
        time consuming for setting up and for connecting to such server (preatty easu in mysql-workbench).
        Faster way to encrypt your mysql confidential data traffic is to use ssh tunnel but there is 
        a limitation here user that needs access to mysql data needs to have ssh access to the server 
        if this is an option just define users with localhost as source like my_db_user@localhost 
        this is safer as you cant guarantee mysql users competence so best practice is to prevent having '%',
        to double secure this method do not to expose 3306 to the public and only allow localhost(unix socket) and 127.0.0.1
        (remember mysqlclient unixsocket/ ip connection) to be able to connect through this port.
        In dockerized mysql instances when I need it to be visible I just do ports config like
        127.0.0.0:3306:3306 then it will be visible to host machine only.
        but if user wont have ssh access to the server then only option you have is using ssl cert.
        So remember having user@'%' or even user@'some_ip' you still without ssl or ssh the traffic from mysql-server
        is still unencrypted.   

Ok comming back to mysql monitoring config:
add client to my.cnf in /etc/mysql or to /etc/mysql/conf.d/mysql.cnf

[client]
user = zabbix
password = zabbixuserpassword
port = 3326
host = 127.0.0.1



add myu.cnf


mkdir -p /var/lib/zabbix/
cd /var/lib/zabbix
ln -sv /etc/mysql/my.cnf


service zabbix-agent restart

Now you can add mysql template items in zabbix server .

select linux templates to see agent visibility


bug in default userparameter_mysql agent file 

cat /etc/zabbix/zabbix_agentd.d/userparameter_mysql.conf 
redirect error to stdout to grep later 

UserParameter=mysql.ping,HOME=/var/lib/zabbix mysqladmin ping 2>&1 | grep -c alive  
previously was
UserParameter=mysql.ping,HOME=/var/lib/zabbix mysqladmin ping | grep -c alive
so grep didnt work


Write your post here.
