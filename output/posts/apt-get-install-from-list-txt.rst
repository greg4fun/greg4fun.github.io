.. title: apt-get install from list txt
.. slug: apt-get-install-from-list-txt
.. date: 2014/10/11 19:37:36
.. tags: 
.. link: 
.. description: 
.. type: text

sudo apt-get install $(cat syspackages.txt)
cat syspackages.txt | xargs sudo apt-get install 
