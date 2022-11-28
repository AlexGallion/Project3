# Project 3

## About The Project

The project that I have made is a Raspberry Pi Internet Speed Monitor. How I achieved this was wirting a python script that interacts with a program called Speedtest CLI from Ookla. This program works by polling the popular speedtest.net service to get your ping, download speed, and upload speed.
The aim of this project was to complete the requirements necessary to completing this project.

Goal's of Project 3:
* Apply skills discussed in class to physical hardware
* Compare and evaluate possible Raspberry Pi projects
* Troubleshoot problems from start to finish
* Practice writing documentation


## Built With

* [Raspberry Pi](www.raspberrypi.org) - Raspberry Pi is a tiny computer a system on a chip which integrates the CPU and GPU in a single integrated circuit, with the RAM, USB ports, and other components soldered onto the board for an all-in-one package. 


## Getting Started

### Prerequisites

Below are all the pieces of equipment that we made use of to set up our Raspberry Pi internet speed monitor.

  * Raspberry Pi
  * Micro SD Card 
  * USB Drive
  * Wi-fi
  

### Installation

1. Before we get started with setting up the Raspberry Pi to monitor the internet’s speed, we must first make sure our Raspberry Pi is up to date.
   ```
   sudo apt-get update
   sudo apt-get upgrade
   ```
2. We now need to install some packages so that we can add the package repository for the Speedtest CLI software.
   ```
   sudo apt install apt-transport-https gnupg1 dirmngr lsb-release
   ```
3. With the packages we need installed we can now add the GPG key for Ookla’s Speedtest repository to the keychain.
   ```
   curl -L https://packagecloud.io/ookla/speedtest-cli/gpgkey | gpg --dearmor | sudo tee /usr/share/keyrings/speedtestcli-archive-keyring.gpg >/dev/null
   ```
4. Next we need to add the Ookla repository to our sources list.
   ```
   echo "deb [signed-by=/usr/share/keyrings/speedtestcli-archive-keyring.gpg] https://packagecloud.io/ookla/speedtest-cli/debian/ $(lsb_release -cs) main" | sudo tee      /etc/apt/sources.list.d/speedtest.list
   ```
5. As we added a new package repository we need to update our package list.
   ```
   sudo apt update
   ```
6. Finally, we can install the official Speedtest CLI to our Raspberry Pi from Ookla.
   ```
   sudo apt install speedtest
   ```
7. We can now test that we have installed the speedtest software to your Raspberry Pi.
   Let us run the following command to start up the speedtest.
   ```
   speedtest
   ```
   
### Writing our Speed Test Python Script
1. We can begin creating our Python script for the Raspberry Pi internet speed monitor by running the following command.
   ```
   cd ~
   nano speedtest.py
   ```
2. Within this file write the following lines of code.
   ```
   import os
   import re
   import subprocess
   import time
   ```
3. This library is what will allow us to track our speed over a length of time.
   ```
   response = subprocess.Popen('/usr/bin/speedtest --accept-license --accept-gdpr', shell=True, stdout=subprocess.PIPE).stdout.read().decode('utf-8')
   ```
4. These three lines of code are fairly simple, and all do the same thing.
   They use the re library to run a regular expression for a certain piece of text and find the number located next to each piece of text.
   ```
   ping = re.search('Latency:\s+(.*?)\s', response, re.MULTILINE)
   download = re.search('Download:\s+(.*?)\s', response, re.MULTILINE)
   upload = re.search('Upload:\s+(.*?)\s', response, re.MULTILINE)
   jitter = re.search('Latency:.*?jitter:\s+(.*?)ms', response, re.MULTILINE)
   ```
5. Using this we will get the values we are after from the output of the Speedtest CLI software that we can write to our CSV file.
   ```
   ping = ping.group(1)
   download = download.group(1)
   upload = upload.group(1)
   jitter = jitter.group(1)
   ```  
6. This bit of code is straightforward. The code is kept within a try statement so that if any errors occur, it will not stop the script from operating.
   ```
   try:
    f = open('/home/pi/speedtest/speedtest.csv', 'a+')
    if os.stat('/home/pi/speedtest/speedtest.csv').st_size == 0:
            f.write('Date,Time,Ping (ms),Jitter (ms),Download (Mbps),Upload (Mbps)\r\n')
   except:
    pass
   ```
7. Finally, we print out all our data separated by commas.
   ```
   f.write('{},{},{},{},{},{}\r\n'.format(time.strftime('%m/%d/%y'), time.strftime('%H:%M'), ping, jitter, download, upload))
   ```
8. Below is an included example of what the output data will look like on the first run of our code.
   ```
   Date,Time,Ping (ms),Jitter (ms),Download (Mbps),Upload (Mbps)
   04/29/21,06:28,18.32,1.21,23.30,7.78
   ```
   
   
## Usage

Final Outcome

<img src="https://github.com/AlexGallion/Project3/blob/main/Outcome.PNG" width="500">


