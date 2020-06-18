.:w
. title: postgres terminate connection
.. slug: postgres-terminate-connection
.. date: 2014/05/13 18:23:47
.. tags:postgres
.. category: Databases
.. link: 
.. description: 
.. type: text

.. code-block:: psql

    select pg_terminate_backend(pid)
    from pg_stat_activity;

