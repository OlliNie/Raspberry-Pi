# ALEXA SDK #

## Task
Install Alexa SDK on the Raspberry pi4.  This is particularly challenging since the 
SDK is not supported on the Raspberry pi4 due to it running on the Buster OS.

## Step 1 (Install Operating System)
Install the Raspbian Buster with desktop and recommended software from 
https://www.raspberrypi.org/downloads/raspbian/

## Step 2 (setup SSH on Raspberry pi to allow remote login ***OPTIONAL)
I like to use my laptop to program my Raspberry Pi.  It however is possible to 
do all programming locally.  This can be done from the terminal and or from the desktop.  If you want to do this from the terminal type

< /br>
Enter `sudo raspi-config` in a terminal window
< /br>
Select `Interfacing Options`

Now that SSH is enabled, in the console of the Raspberry Pi type ifconfig to find the ip address of your Raspberry Pi.


