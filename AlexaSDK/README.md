# ALEXA SDK #

## Task
Install Alexa Voice Service (AVS) on the Raspberry pi4.  This is particularly challenging since the 
SDK is not supported on the Raspberry pi4 due to it running on the Buster OS.

## Step 1 (Install Operating System)
Install the Raspbian Buster with desktop and recommended software from 
https://www.raspberrypi.org/downloads/raspbian/

## Step 2 (setup SSH on Raspberry pi to allow remote login ***OPTIONAL)
I like to use my laptop to program my Raspberry Pi.  It however is possible to 
do all programming locally.  This can be done from the terminal and or from the desktop.  If you want to do this from the terminal type

```
Enter sudo raspi-config in a terminal window
Select Interfacing Options
Navigate to and select SSH
Choose Yes
Select Ok
Choose Finish
```

Now from the terminal of your Raspberry Pi type
```
ifconfig
```
This will give you the IP address of your Raspberry Pi.

Finally you can SSH (remotely access your Raspberry Pi) from you laptop terminal by typing the following into terminal
```
SSH pi@theIpAddressYourPi
```
After hitting enter you will also need to provide the password of your Raspberry Pi

## Installing Alexa Voice Service
As previosly stated atleast as of today 9/24/2019, Amazon is not currently supporting the Aamazon Voice Services on the Raspberry Pi4, this has to do with the fact that 
the new Pi4 runs on the Buster Os, and not Stretch as the previous Pi.  The Voice Service still can be installed on the newer OS using the AVS SDK (Software Development Kit), however
some things need to be modified.



