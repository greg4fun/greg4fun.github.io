.. title: Linux suspended jobs
.. slug: linux-suspended-jobs
.. date: 2014/05/17 19:31:32
.. tags: Linux
.. category: Linux
.. link: 
.. description: 
.. type: text

If you suspended job by accident with ctrl+z you can always resume by fg here more examples:

.. raw:: html

   <kbd>crtl</kbd><kbd>z</kbd> suspend<br>

   jobs - list the current jobs<br>

   fg - resume the job that's next in the queue<br>
   fg %[number] - resume job [number]<br>
   bg - Push the next job in the queue into the background<br>
   bg %[number] - Push the job [number] into the background<br>
   kill %[number] - Kill the job numbered [number]<br>
   kill -[signal] %[number] - Send the signal [signal] to job number [number]<br>
   disown %[number] - you won't be owner of process anymore so it will be alive after leaving terminal<br>
   %vim <br>
