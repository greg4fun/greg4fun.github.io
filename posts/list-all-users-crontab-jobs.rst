.. title: List all users crontab jobs
.. slug: list-all-users-crontab-jobs
.. date: 2014/05/19 11:50:10
.. tags: Linux
.. category: Linux
.. link: 
.. description: 
.. type: text


.. code-block:: bash

   for usr in $(cut -f1 -d: /etc/passwd); do echo $usr; crontab -u $usr -l; done
