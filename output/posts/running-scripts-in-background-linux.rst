.:w
. title: Running scripts in background (linux)
.. slug: running-scripts-in-background-linux
.. date: 2014/05/13 14:01:14
.. tags: Linux, bash
.. category: Linux
.. link: 
.. description: 
.. type: text

I used to execute a lot of commands with & at the end. But once I used plowshare and it didnt work because & works until
you wont log out .So my solution (I know I could use nohup :) ) was screen. Screen is the best tool admin can use when working 
remotely. It can be usefull when you're in place with big packet loss and you often lost connection so when you're
restoring database  you should be in screen session because when something will disconnect you you can alwas log in and
connect to screen session in which restoring is taking place. 

So executing 

.. code-block:: bash

   $./my_script.sh & 

will be only running when your session is still active so when you'll logout it will be killed.

To run it independent from your presence on server do it with nohup:

.. code-block:: bash
   
   $ nohup ./my_script.sh &
   

To launch screen :

.. code-block:: bash

   $ screen


Best practice is to launch screen with its name :

.. code-block:: bash

   $ screen -S my_own_screen_name

.. raw:: html

   Then if you want detach screen to get back later

   <kbd>Ctrl</kbd><kbd>a</kbd><kbd>d</kbd>

   If you want terminate screen just 
   <kbd>Ctrl</kbd><kbd>d</kbd> or exit 

then you can resume it with:

.. code-block:: bash

   $ screen -r my_own_screen_name

To list all available screens :

.. code-block:: bash

   $ screen -ls

To attach to already attached screen:

.. code-block:: bash

   $ screen -x my_own_screen_name

To attach to attached screen and detach others:

.. code-block:: bash

   $ screen -r -d my_own_screen_name

To rename existing screen:

.. raw:: html

   
   (being attached):

   <kbd>Ctrl</kbd><kbd>a</kbd>:sessionname my_screen_name<kbd>Enter</kbd>




