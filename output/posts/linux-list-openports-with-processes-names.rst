.. title: Linux list openports with processes names
.. slug: linux-list-openports-with-processes-names
.. date: 2014/07/02 13:53:47
.. tags: 
.. link: 
.. description: 
.. type: text


.. code-block:: bash

   netstat -putln

-p the PID and name of the program to which each socket belongs.

-u udp

-t tcp

-l only listening sockets.  (These are omitted by default.)

-n numeric port number

.. code-block:: bash

    (Not all processes could be identified, non-owned process info
     will not be shown, you would have to be root to see it all.)
     Active Internet connections (only servers)
     Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
     tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -               
     tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -               
     tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN      -               
     tcp        0      0 127.0.0.1:5433          0.0.0.0:*               LISTEN      -               
     tcp        0      0 0.0.0.0:902             0.0.0.0:*               LISTEN      -               
     tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -               
     tcp        0      0 0.0.0.0:9418            0.0.0.0:*               LISTEN      -               
     tcp        0      0 127.0.0.1:2222          0.0.0.0:*               LISTEN      -               
     tcp        0      0 0.0.0.0:9614            0.0.0.0:*               LISTEN      -               
     tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -               
     tcp        0      0 127.0.0.1:5939          0.0.0.0:*               LISTEN      -               
     tcp6       0      0 :::22                   :::*                    LISTEN      -               
     tcp6       0      0 ::1:631                 :::*                    LISTEN      -               
     tcp6       0      0 :::25                   :::*                    LISTEN      -               
     tcp6       0      0 :::9418                 :::*                    LISTEN      -               
     udp     5376      0 0.0.0.0:5353            0.0.0.0:*                           2934/libpepflashpla
     udp        0      0 0.0.0.0:5353            0.0.0.0:*                           -               
     udp        0      0 0.0.0.0:46482           0.0.0.0:*                           -               
     udp        0      0 0.0.0.0:631             0.0.0.0:*                           -               
     udp6       0      0 :::5353                 :::*                                -               
     udp6       0      0 :::57898                
