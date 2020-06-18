.:w
. title: git_notes
.. slug: git_notes
.. date: 2014/06/04 09:10:34
.. tags: 
.. link: 
.. description: 
.. type: text

git diff --cached     /\ what is about to be committed
git status   brief summary of the situation with 
git log At any point you can view the history of your changes usingggit log -pit log -p

git log  -pIf you also want to see complete diffs at each step, use

git log --stat --summaryOften the overview of the change is useful to get a feel of each step:w

remove wrongly commited large files
➜  my_project git:(master) ✗ git filter-branch --tree-filter 'rm -rf site_media/help' HEAD
Rewrite 22d1cead6a67d68939da2eef48c5372b36651a5c (70/70)
Ref 'refs/heads/master' was rewritten


restore deleted uncommited file
git status
git checkout -- filename



The output tells you what you need to do. git reset HEAD cc.properties etc.

This will unstage the rm operation. After that, running a git status again will tell you that you need to do a git checkout -- cc.properties to get the file back.

Update: I have this in my config file

$ git config alias.unstage
reset HEAD
which I usually use to unstage stuff.
