# ALEXA SDK ON RASPBERRY PI4 RUNNING BUSTER OS #

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
the new Pi4 runs on the Buster OS, and not Stretch as the previous Pi.  The Voice Service still can be installed on the newer OS using the AVS SDK (Software Development Kit), however
some things need to be modified.  To get started, lets refer to the Amazon website at https://developer.amazon.com/docs/alexa-voice-service/input-avs-credentials.html.

Lets first make sure that we have the latest version of all the packages by entering the following command into the terminal. 
```
sudo apt-get upgrade
```
sudo is short for super user do, and gives you abilities you wouldn't otherwise have, for example in order to run the apt-get command you need sudo.  Apt-get performs installations and is the preferred method of managing software with Debian based Linux distributions such as Buster OS.  Finally the upgrade does exactly what you would expect it to do, it upgrades your current packages to the newest version.

Now we are ready to start installing the AVS SDK. Go to the location in which you wish to store everything related to connecting to Alexa, and  run the following commands.
```
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/setup.sh \
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/genConfig.sh \
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/pi.sh
```
wget is used to retrieve content from the web using HTTP/HTTPS protocol.  Now that we have these files we still need to get a config file.  In order to do this follow the link 
listed above to get the config.json file from AVS, however DO NOT follow the next setep "Build the AVS Device SDK" listed on the Amazon website since this is where we are going to run into compatibility issues when running on the Buster OS. 

Now we will need to modify several files.  I personally like using nano to modify files through the terminal, however there are plenty of other options.  In either case lets first
modify the pi.sh file.  I am going to do this from the command line 
```
nano pi.sh
```
Within pi.sh look for libssl1.0-dev and change it tolibssl1.1, also change alsolibgcrypt20-dev to libgcrypt20 and save your modifications.

Now we need to modify the CMakelists.txt file, however it only gets created when you try to build the AVS Device SDK.  Make sure that your config.json file is within the same directory witht the other 3 files that we downloaded using wget.  In the terminal CD into the directory and run the following command in the terminal.
```
sudo bash setup.sh config.json [-s 1234]
```
This will start the SDK build, however it will fail.  No worries now we have access to the CMakelists.txt.  We will also be modifying this file using nano.  
