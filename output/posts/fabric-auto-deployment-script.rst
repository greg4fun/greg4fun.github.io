.. title: fabric - auto deployment script
.. slug: fabric-auto-deployment-script
.. date: 2016-11-02 14:45:29 UTC
.. tags: DevOps, fabric
.. category: DevOps
.. link: 
.. description: 
.. type: text

Recently I wrote fabric deployment scrip maybe someone will find it usefull.

It enables possibility to run "group execute" task with 


.. code-block:: python

    fab live_servers pull restart

or single host 

.. code-block:: python
    
    fab live1 pull

All we need to do is to define group or sinle host as function afterwards I used end update decorator.

I know there could be also something like duplication of tasks with separate servers fab live1 pull live2
pull but I believe that fabric was written for distributed systems which has different paths of apps and users
etc.

also roledefs with extra dict keys didn't work for me)? I want to keep this simple single/multiple host
deployment commands like : fab live_servers pull, fab test pull

.. code-block:: python

    from fabric.api import run, env, local, get, cd
    from fabric.tasks import execute
    import inspect
    import sys
    import os
    import re
    from StringIO import StringIO

    # fabfile author: Grzegorz Stencel
    # usage:
    # run: fab help for examples
    # fab staging svnxapp:app=holdings_and_quotes,layout.py,permissions.py restart
    # fab test svnxlib

    SERVER_BRANCHES = {
        'live': 'master',
        'sit': 'sit',
        'uat': 'uat',
        'live2':'master',
        'live3':'master'

    }
    # MAIN CONF
    SERVERS = {
        'local': {
            'envname': 'local',
            'user': 'greg',
            'host': 'localhost',
            'host_string': 'localhost',
            'path': os.environ.get('SITE_ROOT', '/opt/myapp/test'),
            'www_root': 'http://localhost:8081/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'test': {
            'envname': 'test',
            'user': 'root',
            'host': 'myapp-test.stencel.com',
            'host_string': 'myapp-test.stencel.com',
            'path': '/var/www/myapp/test/',
            'www_root': 'http://myapp-test.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'uat': {
            'envname': 'uat',
            'user': 'myapp',
            'host': 'uat.myapp2.stencel.com',
            'host_string': 'uat.myapp2.stencel.com',
            'key_filename': 'deploy/keys/id_rsa',
            'path': '/opt/myapp/uat/',
            'www_root': 'http://uat.myapp2.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'sit': {
            'envname': 'sit',
            'user': 'myapp',
            'host': 'sit.myapp2.stencel.com',
            'host_string': 'sit.myapp2.stencel.com',
            'key_filename': 'deploy/keys/id_rsa',
            'path': '/opt/myapp/sit/',
            'www_root': 'http://sit.myapp2.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'live': {
            'envname': 'live',
            'user': 'myapp',
            'host': '10.10.10.10',
            'host_string': 'myapp2.stencel.com',
            'path': '/opt/myapp/live/',
            'www_root': 'http://myapp2.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'live2': {
            'envname': 'live2',
            'user': 'root',
            'host': '10.10.10.11',
            'host_string': 'live2.stencel.com',
            'path': '/var/www/myapp/live/',
            'www_root': 'http://myapp2.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },
        'live3': {
            'envname': 'live3',
            'user': 'root',
            'host': '10.10.10.12',
            'host_string': 'live3.stencel.com',
            'path': '/var/www/myapp/live/',
            'www_root': 'http://myapp2.stencel.com/',
            'retries_before_killing': 3,
            'retry_sleep': 2
        },

    }

    LIVE_HOSTS = ['live', 'live2', 'live3']


    def list_hosts():
        """
        Lists available myapp hosts
        """
        print " Single hosts(if you want to pull from svn only to one of them):"
        print '   %s' % '\n   '.join([a for a in SERVERS])
        print " Multiple hosts"
        print '   live (which contains %s)' % ','.join([a for a in LIVE_HOSTS])


    def test():
        """
        single host definition , "fab test restart" wil restart this one host

        """
        env.update(dict(SERVERS['test']))


    def localhost():
        """
        single host definition , "fab test restart" wil restart this one host

        """
        env.update(dict(SERVERS['local']))


    def uat():
        """
        single host definition , "fab uat restart" wil restart this single host

        """
        env.update(dict(SERVERS['uat']))


    def sit():
        """
        single host

        """
        env.update(dict(SERVERS['sit']))


    #  SERVERS GRcompanyS DEFINITION
    def live():
        """
        multiple grcompany of hosts - running: "fab live restart" will restart all live servers

        """
        env['hosts'] = [SERVERS[a]['host'] for a in LIVE_HOSTS]

        # env.update(dict(SERVERS['staging']))


    def env_update(func):
        """
        Decorator - needs to be added to each task in fabricfile - for multiple host task execution
        """

        def func_wrapper(*args, **kwargs):
            if not len(env.hosts):
                return func(*args, **kwargs)
            else:
                env.update(dict(SERVERS[filter(lambda x: SERVERS[x]['host'] == env.host, MyApp_SERVERS)[0]]))
                func(*args, **kwargs)

        return func_wrapper


    @env_update
    def bundle_media():
        """
        bundles media like css and js to one file.
        example:
            fab test bundle_media
        """
        # export DJANGO_SETTINGS_MODULE=settings
        #run("cd {0} && source settings/{1}-config.sh && python scripts/bundle_media.py".format(env.path,env.envname))
       run("source /usr/share/virtualenvwrapper/virtualenvwrapper.sh && workon {0} && python scripts/bundle_media.py".format("%s-myapp" % env.envname if env.envname<> 'live' else 'MyApp-test')) #change live venv to be live-MyApp

    def _valid_branch(env):
        branch = run("cd {0} && git rev-parse --abbrev-ref HEAD".format(env.path))
        return branch == SERVER_BRANCHES[env.envname] and not env.envname=='local'


    @env_update
    def pull(*args, **kwargs):
        if _valid_branch(env):
            with cd(env.path):
                run("git fetch origin")
                run("git reset --hard origin/%s" % branch)
        else:
            print "Error : Server is checked out to wrong branch!!!"


                #run('git fetch --quiet')
                #run('git fetch --tags --quiet')

    @env_update
    def reload():
        """
        Reload specified servers - kills unused gunicorn workers but waits workers with old code to finish processing.

        """
        bundle_media()

        #if env.envname in ('uat', 'staging', 'live'):
        f = StringIO()
        get("/opt/myapp/%s/pid" % env.envname,f)
        pid = re.search(r'\d+',f.getvalue()).group()
        run("ps aux | grep gunicorn | grep %s | grep master | grep -v grep | awk '{print $2}'" % env.envname)
        run("kill -HUP %s" % pid)


    @env_update
    def restart():
        """
        Hard restarts specified servers

        """
        bundle_media()
        run("ps aux | grep gunicorn | grep %s | grep master | grep -v grep | awk '{print $2}'" % env.envname)
        run("supervisorctl stop myapp-%s && supervisorctl start MyApp-%s" % (env.envname,env.envname))
        run("ps aux | grep gunicorn | grep %s | grep master | grep -v grep | awk '{print $2}'" % env.envname)


    def help():
        fabric_functions = ['run', 'execute', 'local', 'func_wrapper']
        functions = set([obj.__name__ if obj.__name__ not in fabric_functions else '' for name, obj in
                         inspect.getmembers(sys.modules[__name__]) if inspect.isfunction(obj)])
        functions.remove('')
        print "usage: \n  fab [host/grcompany of hosts] [commands] (optional command with arguments command:kwarg=val,arg1,arg2,arg3)"
        print "\navailable servers:"
        list_hosts()
        print "\ncommands:\n  %s" % ', '.join([a for a in functions])
        print "\nexamples:\n  staging svnxapp:app=holdings_and_quotes,layout.py,permissions.py restart"
        print "  fab test restart"
        print "  fab staging svnxapp:app=holdings_and_quotes,lib/quote.py,layout.py,models.py"
        print "  fab staging svnxapp:app=holdings_and_quotes,lib/quote.py restart"
        print "  fab test build"
        print "  fab test bundle_media restart"
        print " For svnx whole app (comma in the end):"
        print "  fab test svnxapp:app=medrep,"
        print " For global lib:"
        print "  fab test svnxlib"
        print " For whole global media:"
        print "  fab test svnxmedia:"
        print " For global media file:"
        print "  fab test svnxmedia:javascript"
        print "  fab test svnxmedia:javascript/company/checklist.js"
        print "\nIf .js file in args like : fab staging svnxapp:app=holdings_and_quotes,media/js/quote.js,layout.py,models.py"
        print "It will bundle media itself"
        print "Restart test staging without params:\n  fab restart"
        for f in functions:
            print f
            print globals()[f].__doc__
            print "\n"



    @env_update
    def accessguni():
        run("tail /var/log/myapp/access-%s.log" % env.envname.upper() )

    @env_update
    def accessgunilive():
        run("tail -f /var/log/myapp/access-%s.log" % env.envname.upper() )

    @env_update
    def errorguni():
        run("tail /var/log/myapp/error-%s.log" % env.envname.upper() )

    @env_update
    def errorgunilive():
        run("tail -f /var/log/myapp/error.log" % env.envname.upper() )

    def hostname():
        run('uname -a')

    @env_update
    def uptime():
        run('uptime')


