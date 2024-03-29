# ALEXA SDK ON RASPBERRY PI4 RUNNING BUSTER OS #

## Task
Install Alexa Voice Service (AVS) on the Raspberry pi4.  This is particularly challenging since the 
SDK is not supported on the Raspberry pi4 due to it running on the Buster OS.

It took me some research to figure this all out, and the problem was that a lot of the information was scattered all around the web and not in a single location. This inspired me to create this
write up in order to assist others to get Alexa voice services running on their Raspberry Pi.


## Step 1 (Install Operating System)
Install the Raspbian Buster with desktop and recommended software from 
https://www.raspberrypi.org/downloads/raspbian/

## Step 2 (setup SSH on Raspberry pi to allow remote login ***OPTIONAL)
I like to use my laptop to program my Raspberry Pi.  It, however, is possible to do all programming locally.  This can be done from the terminal and or from the desktop.  If you want to do this from the terminal type

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

Finally, you can SSH (remotely access your Raspberry Pi) from you laptop terminal by typing the following into the terminal
```
SSH pi@theIpAddressYourPi
```
After hitting enter you will also need to provide the password of your Raspberry Pi

## Installing Alexa Voice Service
As previously stated at least as of today 9/24/2019, Amazon is not currently supporting the Amazon Voice Services on the Raspberry Pi4, this has to do with the fact that the new Pi4 runs on the Buster OS, and not Stretch as the previous Pi.  The Voice Service still can be installed on the newer OS using the AVS SDK (Software Development Kit), however,
some things need to be modified.  To get started, let's refer to the Amazon website at https://developer.amazon.com/docs/alexa-voice-service/input-avs-credentials.html.

Lets first make sure that we have the latest version of all the packages by entering the following command into the terminal. 
```
sudo apt-get upgrade
```
sudo is short for superuser do, and gives you abilities you wouldn't otherwise have, for example in order to run the apt-get command you need sudo.  Apt-get performs installations and is the preferred method of managing software with Debian based Linux distributions such as Buster OS.  Finally, the upgrade does exactly what you would expect it to do, it upgrades your current packages to the newest version.

Now we are ready to start installing the AVS SDK. Go to the location in which you wish to store everything related to connecting to Alexa, and run the following commands.
```
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/setup.sh \
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/genConfig.sh \
wget https://raw.githubusercontent.com/alexa/avs-device-sdk/master/tools/Install/pi.sh
```
wget is used to retrieve content from the web using HTTP/HTTPS protocol.  Now that we have these files we still need to get a config file.  In order to do this follow the link listed above to get the config.json file from AVS, however, DO NOT follow the next steps "Build the AVS Device SDK" listed on the Amazon website since this is where we are going to run into compatibility issues when running on the Buster OS. 

Now we will need to modify several files.  I personally like using nano to modify files through the terminal, however, there are plenty of other options.  In either case, lets first
modify the pi.sh file.  I am going to do this from the command line 
```
nano pi.sh
```
Within pi.sh look for libssl1.0-dev and change it tolibssl1.1, also change alsolibgcrypt20-dev to libgcrypt20 and save your modifications.

Now we need to modify the CMakelists.txt file, however, it only gets created when you try to build the AVS Device SDK.  Make sure that your config.json file is within the same directory with the other 3 files that we downloaded using wget.  In the terminal CD into the directory and run the following command in the terminal.
```
sudo bash setup.sh config.json [-s 1234]
```
This will start the SDK build, however, it will fail.  No worries now we have access to the CMakelists.txt.  We will also be modifying this file using nano.  

After the failed installation modify the CMakeLists.txt by adding the following line of code right after the endif() which should be on about line 8.
```
set(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -latomic")
```
Now we should be able to build the AVS Device SDK without any issues.

## Talking to Alexa
 
Before we move on any further at this time it is important to make sure that both your microphone and speakers are working properly.
Without these devices, you will get an error and at best you will be operating in limited functionality mode.  

A good article that goes into setting up a microphone and speaker on the raspberry pi is https://iotbytes.wordpress.com/connect-configure-and-test-usb-microphone-and-speaker-with-raspberry-pi/ .

Now the sound setup can actually be a bit tricky since everyone has different audio devices.   This is where most of my troubles came from.   If you have sound working lets try to get a refresh token,
and if that doesn't work, then we can further troubleshoot what is going on with the sound.

# Get refresh token
Cd into the directory that we have been working in which should be something along the lines of cd /home/pi/.  Make sure that you see a file named startsample.sh and run the following bash command.
```
sudo bash startsample.sh
```
IF all goes well, you should see a script that keeps on running that asks for authorization and a link to an AWS website where you can do that using a unique password that shows up in the terminal.
If something doesn't go right, the script will stop looking for authorization and error out.  In this case, it is probably sound related.  You can check the errors and continue troubleshooting from here.
For me, I had to do additional steps which I will go into detail below.  Once you are autohrized you will be able to talk to Alexa just as if you were talking to Alexa using one of those AWS Echos.
Refer to the AWS website for more details at https://developer.amazon.com/docs/alexa-voice-service/get-a-refresh-token.html 


## IF you ran into sound issues, this should help.

copy the following code to these locations.  You may need to create these files before adding the code listed below.  
* /home/pi/.asoundrc
* /root/.asoundrc
* /home/pi/third-party/alexa-rpi/config/asound.conf 

```
pcm.dsnooper {
    type dsnoop
    ipc_key 816357492
    ipc_key_add_uid 0
    ipc_perm 0666
    slave {
        pcm "hw:1,0"
        channels 1
    }
}
pcm.dmixer {
    type dmix
    ipc_key 1024
    slave {
        pcm "hw:1,0"
        period_time 0
        period_size 1024
        buffer_size 8192
        rate 44100
    }

    }
pcm.!default {
        type asym
        playback.pcm {
                type plug
                slave.pcm "dmixer"
        }
        capture.pcm {
                type plug
                slave.pcm "dsnooper"
        }
}
```

## Additional notes

If you have any issues installing Alexa onto your Raspberry Pi4 running on Buster, or if you have anything that you can add, then please create a PR or create an issue! I hope this was
helpful :)


Here are all the links to that I referred to in orderto come up withthis step by step guide.
* https://github.com/alexa/avs-device-sdk/issues/1508
* https://github.com/alexa/avs-device-sdk/issues/943
* https://developer.amazon.com/docs/alexa-voice-service/input-avs-credentials.html
* https://github.com/alexa/avs-device-sdk/issues/1405
* https://github.com/alexa/avs-device-sdk/issues/1413
* https://github.com/alexa/avs-device-sdk/issues/1404
* https://github.com/alexa/avs-device-sdk/issues/743
* https://github.com/alexa/avs-device-sdk/issues/1366
* https://developer.amazon.com/docs/alexa-voice-service/set-up-raspberry-pi.html
* https://developer.amazon.com/docs/alexa-voice-service/get-a-refresh-token.html
* https://iotbytes.wordpress.com/connect-configure-and-test-usb-microphone-and-speaker-with-raspberry-pi/

