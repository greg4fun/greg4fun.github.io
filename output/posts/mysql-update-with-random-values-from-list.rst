.. title: mysql update with random values from list
.. slug: mysql-update-with-random-values-from-list
.. date: 2014/10/28 19:57:25
.. tags: MySQL, Databases
.. category: Databases
.. link: 
.. description: 
.. type: text

“UPDATE book set year = ELT(1+FLOOR(RAND()*3), 2012,2013,2014)”

