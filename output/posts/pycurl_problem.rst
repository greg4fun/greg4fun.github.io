.:w
. title: pycurl_problem
.. slug: pycurl_problem
.. date: 2014/06/01 14:43:33
.. tags: 
.. link: 
.. description: 
.. type: text

.. code-block:: bash
   
   (flats)➜  projects  pip install pycurl
    Downloading/unpacking pycurl
      Downloading pycurl-7.19.3.1.tar.gz (116kB): 116kB downloaded
      Running setup.py (path:/workspace/virtualenvs/flats/build/pycurl/setup.py) egg_info for package pycurl
        Traceback (most recent call last):
          File "<string>", line 17, in <module>
          File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 563, in <module>
            ext = get_extension()
          File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 368, in get_extension
            ext_config = ExtensionConfiguration()
          File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 65, in __init__
            self.configure()
          File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 100, in configure_unix
            raise ConfigurationError(msg)
        __main__.ConfigurationError: Could not run curl-config: [Errno 2] No such file or directory
        Complete output from command python setup.py egg_info:
        Traceback (most recent call last):

      File "<string>", line 17, in <module>

      File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 563, in <module>

        ext = get_extension()

      File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 368, in get_extension

        ext_config = ExtensionConfiguration()

      File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 65, in __init__

        self.configure()

      File "/workspace/virtualenvs/flats/build/pycurl/setup.py", line 100, in configure_unix

        raise ConfigurationError(msg)

    __main__.ConfigurationError: Could not run curl-config: [Errno 2] No such file or directory

    ----------------------------------------
    Cleaning up...
    Command python setup.py egg_info failed with error code 1 in /workspace/virtualenvs/flats/build/pycurl
    Storing debug log for failure in /home/greg/.pip/pip.log
    (flats)➜  projects  pip install pycurl

Solution:

.. code-block:: bash

   apt-get install libcurl4-gnutls-dev librtmp-dev
