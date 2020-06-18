.. title: Python combine dates , current date with different hour
.. slug: python-combine-dates-current-date-with-different-hour
.. date: 2014/06/04 16:38:10
.. tags: python
.. category: python
.. link: 
.. description: 
.. type: text

Specification:
I want to launch deployment script but not before 18:00 so every user can leave home.
I want to lunch it today but with different time that datetime.datetime.now() gives
To do it I think best way is t to combine **date.today** with **time(18,00)** and then compare 
with **datetime.now()**
Command of lauching is up to us so jsut for tests lets pass it in argvs:

.:w
. code-block:: bash
   
   python testtime.py 16 35

.. code-block:: python

    import sys
    from datetime import datetime, date,time

    current_time=datetime.now()
    ehour=int(sys.argv[1])
    eminute=int(sys.argv[2])

    exe_time=datetime.combine(date.today(), time(ehour,eminute))
    #or datetime.(datetime.today().year,datetime.today().month,datetime.today().day,ehour,eminute)
    print "time left:"
    print (exe_time - current_time)

    if exe_time<=current_time:
        print "executing..."
