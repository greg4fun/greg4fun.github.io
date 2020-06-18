.. title: Fabric
.. slug: fabric
.. date: 2014/06/08 14:11:35
.. tags: DevOps, fabric
.. category: DevOps
.. link: 
.. description: 
.. type: text

fabric execution:::::
fab -H me@host1,me@host2,me@host3 function

.. raw:: html
    
   Example:
   <small>
   fab -H greg@mmyserver.com get_backup
   </small>

or alternatively:

.. raw:: html
    
   Example:
   <small>
   fab production1 deploy
   </small>

    but then you'll have to production1 defined inside your fabfile.py

.. code-block:: python

   def production():
        env.update(dict(
            dest='production',
            hosts=['some_ip_address'],
    ))

    def development():
        env.update(dict(
            dest='development',
            hosts=['localhost'],
    ))



**local** - execute a local command means host from which we launch fabric
 
**run**   - execute a remote command on all specified hosts, user-level permissions

**sudo** - sudo a command on the remote server)

**put** - copy over a local file to a remote destination)

**get** - download a file from the remote server)

**prompt** - prompt user with text and return the input (like raw_input))

**reboot** - reboot the remote system, disconnect, and wait for wait seconds)

Download some logs

get(remote_path="/tmp/log_extracts.tar.gz", local_path="/logs/new_log.tar.gz")




