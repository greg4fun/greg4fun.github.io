.. title: ssh reverse tunneling
.. slug: ssh-reverse-tunneling
.. date: 2014/06/16 15:07:02
.. tags: ssh, Linux, reverse ssh tunnel
.. category: Linux
.. link: 
.. description: 
.. type: text

When you want someone to connect to your NAT-ed or Firewalled server and 

you run out of ideas ond options or you even dont have

you dont have access to Firewall or VPN server to configure 

anything - you can always try to use reverse ssh tunnel.

The only one condition is that you must be able to ssh from your server to public host( the one other guy wants to connect) 

This is how it works:

[Server]  <----> [ FIREWALL/NAT SERVER ] <----> [ BOB HOST with public ip 80.80.80.80]

