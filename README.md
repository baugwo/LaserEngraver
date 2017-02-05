engravR 0.1
===========

Raspberry Pi Powered Laser Engraver
Powered by Python 2.7

See www.youtube.com/watch?v=xxQ33cNIXxU for a tutorial/intro video.

See http://makezine.com/projects/engravr-a-pi-powered-laser-engraver/ for a more in depth tutorial and more information.

This software allows for the control of a laser engraver with a Raspberry Pi.  Details on the construction of such a machine can be found here: http://funofdiy.blogspot.com/2013/10/a-raspberry-pi-controlled-mini-laser.html

This code is based off of the code found there by Xiang Zhai.

G code interpreter and executer for 2D CNC laser engraver using Raspberry Pi             
Xiang Zhai,   Oct 1, 2013                                                              
zxzhaixiang at gmail.com    														   
Modifications for terminal interface and Use with GCodeTools by Ian D. Miller          
Jan 7,2014			http://www.pxlweavr.com											   
info [at] pxlweavr.com			
Feb 6, 2017, Dutchpatriot
A little tweaked version with the etension for Inkscape 0.48 included. 

Copy the files in the Inkscape0_48_extension folder to /usr/share/inkscape/extensions and restart the inkscape program, than under the extensions you should see 
a Laserengraver extension... Type stuff, draw something and dont forget before you use the extension to select what you want to engrave (usually all so SHIFT-A) and 
than click the path and first option BOJECT TO PATH (or SHIFT-CTRL-C) than use the extension...

Some minor additions to the program, it now tells you when it started, what the engraving speed was, at the end it states 
Time started, time ended, duration, engraving speed and it shuts down the laser pin... I had the issue that after the program stopped that the laser had a
stationary light, dim but on...

I also embedded a main switch, on that you could connect a mosfet or power transistor (one that can take the power of the whole engraver incl. laser) so the whole thing shuts down 
completely...

I also added a cron_laser.sh that you could use to repeat engraving jobs. Wood or leather come out better when you engrave them a few times.
Because I am lazy and like easy I made it a job for the computer instead of having to repeat it 4 or more times...


Have Fun...
									  
Dutchpatriot


Overview
========

This software includes three main sections:

-The "server" code, to run on the pi.  This consists of the following files:

  -laserengraver
  
  -Bipolar_Stepper_Motor_Class.py
  
  -Motor_control.py
  
-The "test" code, which is a simple mini script that can be used to debugging:

  -steppertest.py
  
  -This functionality can also be obtained with laserengraver -m.
    
-The "client" code, which is a simple GUI to control the engraver remotely:

  -remoteengrave.py
  
  -Requires the pexpect library
    
Server Setup
============

-Put the three "server" files on the pi somewhere, probably in the home folder.  For the purposes of this tutorial, I'll assume that files are in a folder called engravR in the /home/pi directory.

-Run following commands:

    cd engravR
    sudo chmod +x laserengraver
    
-Open laserengraver and edit the following lines to reflect the pin numbers you are using:

```
Main_Switch =3

MX=Bipolar_Stepper_Motor(22,18,24,26);     #pin number for a1,a2,b1,b2.  a1 and a2 form coil A; b1 and b2 form coil B

MY=Bipolar_Stepper_Motor(10,8,12,16);       

Laser_switch=5;
```

-Now, we need to add laserengraver to the root $PATH to make calling laserengraver easier.  Run:

    sudo nano /root/.bashrc
    
-Add the following two lines:

    alias sudo='sudo env PATH=$PATH'
    export PATH=/home/pi/engravR:$PATH
    
-Save and exit nano.

-Now, try running:

    sudo -i laserengraver -h

-The following should display:

```
usage: laserengraver [-h] (-f FILEPATH | -m) [-s SPEED]

RPi Controlled Laser Engraver

optional arguments:
  -h, --help            show this help message and exit
  -f FILEPATH, --filepath FILEPATH
                        Path to GCode file
  -m, --manual          Manually control printer
  -s SPEED, --speed SPEED
                        Engraving speed (mm/sec)
```

Now, the terminal interface is ready to go.  If you are comfortable with using the cli via ssh to control the pi, you can probably stop here.  If you would like a client GUI, proceed

To set this up, we need to do one more thing on the Raspberry Pi itself.  Follow the instructions here: http://elinux.org/RPi_Advanced_Setup#Setting_up_for_remote_access_.2F_headless_operation to configure ssh.  On step 2, follow the instructions for installing avahi/zeroconf.  Now, the Pi is ready to go.

Client Setup
============

-Put the remoteengrave.py file somewhere on the client machine.

-Open remoteengrave.py with your favorite text editor.  Look at the folowing lines:

```
#User set variables
remdir = "/home/pi/engravingfiles/"
address = "raspberrypi.local"
user = "pi"
password = "your-password-here"
```

-Set remdir to whatever directory you want GCode files uploaded to.

-Address is the IP address of the pi (raspberrypi.local with avahi-daemon installed)

-User and password are both fairly self-explanatory
  
-Install pexpect however you want.  I suggest using:

    sudo pip install pexpect

-Run

    ./remoteengrave.py
    
-You may also be able to just double click on remoteengrave.py.  A GUI will then open.  It's usange is fairly straightforward.  The server and client will need to be on the same network.
- In tweaked this now creates log files with unique time stamp and filename
Generating GCode
================

-This code is designed to process GCode generated by the GCodeTools Inkscape extension using the "Default" tool.  See http://www.cnc-club.ru/forum/viewtopic.php?f=15&t=35&start=0 for information about GCodeTools, its installation, and its usage.  GCode generated by other software may not work well or at all and require code modification.
# LaserEngraver
# LaserEngraver
