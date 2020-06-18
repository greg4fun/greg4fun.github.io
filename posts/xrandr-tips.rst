.. title: xrandr tips
.. slug: xrandr-tips
.. date: 2015/01/13 20:34:57
.. tags: xrandr, Linux
.. link: 
.. description: 
.. type: text
 

adding resolution 
1)generate mode
cvt 2560 1440 60
➜  blog git:(website) ✗ cvt 2560 1440 60
# 2560x1440 59.96 Hz (CVT 3.69M9) hsync: 89.52 kHz; pclk: 312.25 MHz
Modeline "2560x1440_60.00"  312.25  2560 2752 3024 3488  1440 1443 1448 1493 -hsync +vsync

2)add it to xrandr
sudo xrandr --newmode "2560x1440_60.00"  312.25  2560 2752 3024 3488  1440 1443 1448 1493  -hsync +vsync
setting resolution

3)sudo xrandr --addmode VGA-0 "1680x1050_60.00"
 
xrandr -q 
xrandr --verbose

xrandr --output HDMI-0 --mode 2560x1440

irst generate a "modeline" by using cvt
Syntax is: cvt width height refreshrate

cvt 1680 1050 60
this gives you:

# 1680x1050 59.95 Hz (CVT 1.76MA) hsync: 65.29 kHz; pclk: 146.25 MHz
Modeline "1680x1050_60.00"  146.25  1680 1784 1960 2240  1050 1053 1059 1089 -hsync +vsync
Now tell this to xrandr:

sudo xrandr --newmode "1680x1050_60.00"  146.25  1680 1784 1960 2240  1050 1053 1059 1089 -

First clone the two screens, (the smaller screen will display the top left portion of the virtual screen)

 xrandr --output VGA --auto --right-of LVDS

 xrandr --output LVDS --mode 1280x800

 xrandr --output LVDS --mode 1280x800 --rate 75

 xrandr --output LVDS --auto

 xrandr --output LVDS --off --output HDMI-0 --auto


 xrandr --output VGA1 --mode 1024x768 --rate 60

#Laptop right extra Monitor Left
 
 xrandr --output VGA1 --left-of LVDS1

#Laptop left extra Monitor right
 
 xrandr --output LVDS1 --left-of VGA1

#This is to set your primary monitor.

#This sets your laptop monitor as your primary monitor. 
 
 xrandr --output LVDS1 --primary

#This sets your VGA monitor as your primary monitor. 
 
 xrandr --output VGA1 --primary


 xrandr --output VGA1 --mode 1024x768 --rate 60
 
xrandr --pos <x>x<y>

 $ xrandr --left-of <output>

 $ xrandr --right-of <output>

 $ xrandr --above <output>

 $ xrandr --below <output>

Option '-pos' is more flexible which can place output to anywhere, for example:

$ xrandr --output VGA1 --pos 200x200
$ xrandr --output LVDS1 --pos 400x500

xrandr -o right

