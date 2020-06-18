.. title: Quick vi / vim tutorial
.. slug: vim
.. date: 2014/04/24 14:05:12
.. tags: vim,python
.. category: VIM
.. link: 
.. description: 
.. type: text



This site is written to promote using VIM cause it's free, it has a lot
of plugins and it can even be python IDE ;) and vi is also on every unix
like system so logging to your clean instalation server it's good to
know how to edit conf files.

If you don't want to read more on this site just have a look at basic
commands which will be enough to edit and save file:

**Remember** all this commands should be executed within normal mode to
do so press ESC.

.. raw:: html
    
    <kbd>:</kbd><kbd>q</kbd> <kbd>&#8629;</kbd>QUIT (being in normal mode to be sure you can always use <kbd>ESC</kbd> <kbd>:</kbd><kbd>q</kbd><kbd>&#8629;</kbd>) most important command remember it to avaoid being character from famous "VIM quit joke" how to generate cat /dev/urandom: <br>
    <kbd>:</kbd><kbd>q</kbd><kbd>!</kbd> <kbd>&#8629;</kbd>quit without saving <br>
    <kbd>:</kbd><kbd>w</kbd><kbd>q</kbd><kbd>&#8629;</kbd> - save and quit<br>
    <kbd>y</kbd><kbd>y</kbd> - copy line<br>
    <kbd>p</kbd> - paste line<br>
    <kbd>i</kbd> - insert mode after that you're no longer in normal mode you can than start typig text<br>
    <kbd>h</kbd> <kbd>j</kbd> <kbd>k</kbd> <kbd>l</kbd> - left down up right or you can use arrows in vim<br>
    you can also find undo <kbd>u</kbd> nad redo <kbd>ctrl</kbd><kbd>r</kbd> useful<br>


I was inspired by `Onjin <https://github.com/onjin/vim-startup>`__ when I
saw him typing in vim I was impressed of how his thoughts became text
and functions in VIM so fast without even using a mouse :).

It takes a while to switch from another editor and get its habits (it
took one month for me and I tried 3 times) but after all you will be
happy using lightweight VIM.

To use vim you need to know that there are \ **five modes** (normal,
insert, visual, select, command-line, Ex-mode)

There are 4 main modes you'll be using probably:

-  **Normal** - usually VIm starts in this mode where you can navigate
   through whole file and modify text. To get to this mode press ESC.
-  **Insert** : this mode is like it says - for inserting new text you
   can go to this mode by pressing i or a (being in normal mode ESC)
-  **Visual** : you can select text in this mode by pressing v (being in
   normal mode ESC) and moving for selection
-  **Command-line** : you activate it by pressing : (being in normal
   mode ESC)

.. raw:: html
 
   <div class="alert alert-info">Tip: Capital letters like <kbd>A</kbd> need to followed by <kbd>Shift</kbd> </div>
    

Editing (enter commands below being in normal mode):
====================================================
 
 .. raw:: html
    
    <kbd>i</kbd>  -  enter insert mode and place cursor at <b>current position</b> to start typing<br><br>

 |image0|

--------
 
 .. raw:: html

    <kbd> I </kbd> - enter insert mode and place cursor at <b>start of the line</b> to start typing
 
 |imageI|

--------
 
 .. raw:: html
    
    <kbd>a</kbd> - enter insert mode and place cursor <b>after current position</b>\ to start typing<br><br>

 |image5|

--------

 .. raw:: html

    <kbd>A</kbd> - enter insert mode and place cursor at the <b>end of line</b> to start typing<br><br>

 |image5A|

--------

 .. raw:: html

    <kbd>e</kbd><kbd>a</kbd> - append at end of word<br><br>

 |image6|

--------

 .. raw:: html
    
    <kbd>r</kbd> - replace a single character (does not use insert mode)

--------

 .. raw:: html
    
    <kbd>J</kbd> - join line below to the current one<br><br>
 
 |image7|
 
--------
 
 .. raw:: html
    
    <kbd>c</kbd><kbd>c</kbd> - change (replace) an entire line

--------
 
 .. raw:: html
  
    <kbd>c</kbd><kbd>w</kbd> - change (replace) to the end of word
 
--------
 
 .. raw:: html
    
    <kbd>c</kbd><kbd>$</kbd> - change (replace) to the end of line
 
--------
 
 .. raw:: html
    
    <kbd>s</kbd> - delete character at cursor and subsitute text
 
--------
 
 .. raw:: html
    
    <kbd>S</kbd> - delete line at cursor and substitute text (same as cc)
    
--------

 .. raw:: html
 
    <kbd>d</kbd><kbd>$</kbd>  or<kbd>D</kbd> - delete after cursor


 
 .. raw:: html
    
    <kbd>xp</kbd> - transpose two letters (delete and paste, technically)
 
--------
 
 .. raw:: html 
    
    <kbd>u</kbd> - undo
 
--------
 
 .. raw:: html
  
    <kbd>Ctrl</kbd><kbd> r</kbd> - redo 
 
--------
 
 .. raw:: html
    
    <kbd>.</kbd> - repeat last command
 
--------
 
 .. raw:: html

    <kbd>ESC</kbd> - exit insert mode

--------

Indedation
----------

 .. raw:: html

    <p class="bg-success">
     Examples:<br><br>
     
       :s/\n/ /g - replace all spaces with enter in current line<br>
       :%s/\n/ /g -replace all spaces with enter in all lines<br>
       :%s/\n/ /cg - replace all spaces with enter in all lines but with confirmation<br>

     </p>
      

--------
 

Indedation
----------

 .. raw:: html

    <kbd>v</kbd> then select block you want indent and press number of indedations<kbd>2</kbd><kbd>></kbd> -  execute indentation on the selected block





Marking text (visual mode)
--------------------------

 .. raw:: html

    <kbd>v</kbd> - mark lines with starting visual mode

--------
 
 .. raw:: html

    <kbd>d</kbd> - (after starting selection with v ) cut selected text

--------
 
 .. raw:: html

    <kbd>y</kbd> - (after starting selection with v ) copy (yank) selected text

--------
 
 .. raw:: html

    <kbd>p</kbd> - (after cutting or copying selection) paste selected text after cursor

--------
 
 .. raw:: html

    <kbd>P</kbd> - (after cutting or copying selection) paste selected text before cursor example of all 4 commands above:<br><br>

 |image8|

--------
 
 .. raw:: html

    <kbd>V</kbd> - start Linewise visual mode<br><br>

 |image9|

--------
 
 .. raw:: html

    <kbd>o</kbd> - move to other end of marked area<br><br>

 |image10|

--------
 
 .. raw:: html

    <kbd>Ctrl</kbd><kbd>v</kbd> - start visual block mode

--------
 
 .. raw:: html

    <kbd>O</kbd> - move to Other corner of block<br><br>

 |image11|

--------
 
 .. raw:: html

    <kbd>a</kbd><kbd>w</kbd> - mark a word (being in visual mode)<br><br>

 |image12|

--------
 
 .. raw:: html

    <kbd>a</kbd><kbd>b</kbd> - a () block (with braces) (or vab from normal mode)<br><br>

 |image13|

--------
 
 .. raw:: html

   <kbd>a</kbd><kbd>B</kbd> - a {} block (with brackets)<br><br>

 |image14|

--------
 
 .. raw:: html

    <kbd>i</kbd><kbd>b</kbd> - inner () block

--------
 
 .. raw:: html

    <kbd>i</kbd><kbd>B</kbd> - inner {} block

--------
 
 .. raw:: html

    <kbd>&gt;</kbd> - shift right

--------
 
 .. raw:: html

    <kbd>&lt;</kbd> - shift left

--------
 
 .. raw:: html

    <kbd>y</kbd> - yank (copy) marked text

--------
 
 .. raw:: html

    <kbd>d</kbd> - delete marked text

--------
 
 .. raw:: html

    <kbd>~</kbd> - switch case

Layout/windows:
---------------

 .. raw:: html

    <kbd>Ctrl</kbd><kbd>w</kbd><kbd>w</kbd> - swith between windows

--------
 
 .. raw:: html

    <kbd>Ctrl </kbd><kbd>w </kbd><kbd>&#8679;</kbd> - move cursor up a window example using ctr ww and ctr w arrows in example before switching windows ESC was pressed to enter <b>normal</b> mode:<br><br>

 |image15|

--------
 
 .. raw:: html

    <kbd>ctrl </kbd><kbd>w +</kbd> - increase size of current window

--------
 
 .. raw:: html

    <kbd>ctrl</kbd><kbd>w</kbd><kbd>-</kbd> - decrease size of current window<br><br>

 |image16|

--------
 
 .. raw:: html

    <kbd>ctrl</kbd><kbd>w</kbd><kbd>\</kbd><kbd>_</kbd> - maximize current window

--------
 
 .. raw:: html

    <kbd>ctrl</kbd><kbd>w</kbd><kbd>=</kbd> -  make all equal size<br><br>

 |image17|

--------
 
 .. raw:: html

    <kbd>1</kbd><kbd>0</kbd><kbd>ctrl</kbd><kbd>w</kbd><kbd>+</kbd> - increase window size by 10 lines

--------
 
 .. raw:: html

    <kbd>:vsplit file</kbd> - vertical split

--------
 
 .. raw:: html

    <kbd>:sview</kbd> - same as split, but readonly

--------
 
 .. raw:: html

    <kbd>:hide</kbd> - close current window

--------
 
 .. raw:: html

    <kbd>:only</kbd> - keep only this window open

--------
 
 .. raw:: html

    <kbd>:ls</kbd> - show current buffers

--------
 
 .. raw:: html

    <kbd>:b 2</kbd> - open buffer #2 in this window

Movement:
---------

 .. raw:: html

    <kbd>w</kbd> - next word by punctation <br><br>
 
 |image20|   
 
--------

 .. raw:: html

    <kbd>W</kbd> - next word by spaces<br><br>

 |image21|
 
--------

 .. raw:: html

    <kbd>b</kbd> - back word by punctation
 
--------

 .. raw:: html

    <kbd>B</kbd> - back word by spaces
 
--------

 .. raw:: html

    <kbd>e</kbd> - end word by punctation
 
--------

 .. raw:: html

    <kbd>E</kbd> - end word by spaces
 
--------

 .. raw:: html

    <kbd>h</kbd> - move left
 
--------

 .. raw:: html

    <kbd>j</kbd> - move down
 
--------

 .. raw:: html

    <kbd>k</kbd> - move up
 
--------

 .. raw:: html

    <kbd>l</kbd> - move right
 
--------

 .. raw:: html

    <kbd>^</kbd> - first non-blank character of line
 
--------

 .. raw:: html

    <kbd>)</kbd> - zero) start of line
 
--------

 .. raw:: html

    <kbd>$</kbd> - end of line
 
--------

 .. raw:: html

    <kbd>G</kbd> - Go To command (prefix with number 4j moves down 4 lines.

--------

Operations on file:
-------------------

 .. raw:: html

    <kbd>:e</kbd> - open file in new buffer
 
--------

 .. raw:: html

    <kbd>:w</kbd> - write file to disk - save

--------

 .. raw:: html

    <kbd>:w filename</kbd>  save as

--------

 .. raw:: html

    <kbd>ZZ</kbd> - write file to disk and quit like :wq

--------

 
 .. raw:: html

    <kbd>:wq</kbd> - save file and quit

--------

 
 .. raw:: html

    <kbd>:n</kbd> - edit a new file

--------

 .. raw:: html

    <kbd>:n!</kbd> - edit new file without saving current changes

--------

 .. raw:: html

    <kbd>:q!</kbd> - quit without changes

--------

 .. raw:: html

    <kbd>:q</kbd> - quit editing file

--------

 .. raw:: html

    <kbd>:e .</kbd> - directory explorer

--------

 .. raw:: html

    <kbd>:split</kbd> - filename - split window and load another file

--------

 .. raw:: html

    <kbd>:sview file</kbd> - same as split, but readonly 

--------

Split window:

 .. raw:: html

    <kbd>Ctrl</kbd><kbd>W</kbd><kbd>s</kbd> - horizontal splitting<br>
    <kbd>Ctrl</kbd><kbd>W</kbd><kbd>v</kbd> - for vertical splitting<br>
    <kbd>Ctrl</kbd><kbd>w</kbd><kbd>q</kbd> - close window<br>


Split window:
repeat insert in blockview
ctrlv - select block 
then I -text ESC


Macros - recording
==================

.. |image0| image:: /images/i.gif
.. |image1| image:: /images/a.gif
.. |image2| image:: /ea.gif
.. |image3| image:: /images/J2.gif
.. |image4| image:: /images/i.gif
.. |image5| image:: /images/a.gif
.. |image5A| image:: /images/A.gif
.. |image6| image:: /images/ea.gif
.. |image7| image:: /images/J2.gif
.. |image8| image:: /images/yankpaste.gif
.. |image9| image:: /images/VisualLine.gif
.. |image10| image:: /images/o.gif
.. |image11| image:: /images/visualblock.gif
.. |image12| image:: /images/select_word_2.gif
.. |image13| image:: /images/mark_brackets.gif
.. |image14| image:: /images/selecr_brackets_6.gif
.. |image15| image:: /images/ctrlww.gif
.. |image16| image:: /images/rezise_window.gif
.. |image17| image:: /images/resize_window2.gif
.. |image18| image:: /images/w_2.gif
.. |image19| image:: /images/W_1.gif
.. |image20| image:: /images/w_2.gif
.. |image21| image:: /images/W_1.gif
.. |imageI| image:: /images/I.gif
    
