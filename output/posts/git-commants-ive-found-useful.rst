.. title: GIT commants I've found useful
.. slug: git-commants-ive-found-useful
.. date: 2016-11-03 10:46:51 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text



Check files changed between branches

.. code-block:: bash

    git diff --name-status master..devel

Check changes on file form different branch/commit

.. code-block:: bash

    git diff commit_hash -- filename

Same as above between 2 branches/commits

.. code-block:: bash

    git diff commit_hash master -- filename


Check full file history

.. code-block:: bash

    git log -p -- filename

Check who broke production server :

.. code-block:: bash

    git blame filename

Merge as one commit (need to commit afterwards) its not default like in normal merge:

.. code-block:: bash

    git merge --squash branch

List of commits in git local storage

.. code-block:: bash

    git reflog

Take(checkout) file from different branch/commit

.. code-block:: bash

    git checkout develop -- filename
    git checkout commit_hash -- filename


Reset current branch to remote:

.. code-block:: bash

    git reset --hard origin/current_branch
    git reset --hard origin/master

Save and depracate changes which were not commited

.. code-block:: bash

    git stash 
    git stash save -a

Restore stash (by picking selected)

.. code-block:: bash

    git stash list
    git stash pop {0}


