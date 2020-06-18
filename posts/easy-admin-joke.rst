.. title: Easy admin joke
.. slug: easy-admin-joke
.. date: 2014/05/10 12:31:04
.. tags: Linux
.. category: Linux, Bash
.. link: 
.. description: 
.. type: text

When I first got admin access ages ago on shared server I wanted to do something funny
to my coleague so I thought to display something on his screen. 

Remembering everything in Linux filesystem is a file or a directory 
I read about pseudoterminals "pts"  they are inside /dev directory

Yea yea I know 3 rules of admin : Respect the privacy of others. Think before you type. With great power comes great responsibility bla bla bla ...

To do this joke just check who is logged in and on which pts is he :

.. code-block:: bash

   $ who
   greg     pts/3        2012-03-10 1:26 (:0)
   mark     pts/5        2012-03-10 10:29 (:0)
   jenny    pts/7        2012-03-10 13:06 (:0)


So now, we know that Mark is working on pts/5 so lets send him message :)

.. code-block:: bash

   $ echo "In 5 minutes in toilet /Jenny" > /dev/pts/5

To "clean evidences" :D ( remember that guy working on this terminal will lost his current works he's working on this
specific terminal)

.. code-block:: bash

   $ cat /dev/urandom > /dev/pts/5 

