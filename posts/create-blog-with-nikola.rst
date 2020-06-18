.. title: create blog with nikola
.. slug: create-blog-with-nikola
.. date: 2014/07/12 20:00:31
.. tags: 
.. link: 
.. description: 
.. type: text

Sometimes blogs are not so bad to write your thoughts. This is good place to come back for solution we've found previously spending hours searching for it .

If we want to have more options to edit blog style or anything or host it on our server or github there are a lot of
blog generators .
One of them is Nikola 
.. link:http://getnikola.com/

First of all just install nikola with pip on your system (best way is to use python virtualenv for python applications)

.. code-block:: bash

  pip install nikola

then start new blog 

.. code-block:: bash
   
   nikola init my_blog

You can choose between themes http://themes.getnikola.com/
to list available themes

.. code-block:: bash
   
   nikola install_theme -l  

there is also possibility to use bootswatch theme http://bootswatch.com/
remember not all themes works with bootswatch

.. code-block:: bash

   nikola install_theme bootstrap3-jinja
   nikola bootswatch_theme -n custom_theme -s amelia -p bootstrap3-jinja

