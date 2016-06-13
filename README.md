# Pypr Robots
Paper Prototyping of Social Robots for human-robot interaction.


## Setting Up Raspbian on Raspberry Pi
Note: You can use the instructions provided [here](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)
or you can use the instructions provided below if you are using Ubuntu.

Install Raspbian from [here](https://www.raspberrypi.org/downloads/raspbian/) (the full version, not Lite version, otherwise ssh won't be setup for us).  
Inside the zip file should be a single `.img` file. Unzip the zip file.  
Plug in your Raspberry Pi's micro SD card into your computer.  
Run the program 'Disks' and locate the micro SD card.  
Click the 'gear' on the top right and then click 'Restore Disk Image'  
![alt text](images/Install-01.png "Restore Disk Image")  
Navigate to the `.img` file that we downloaded, then click 'Start Restoring...'.  
![alt text](images/Install-02.png "Image to Restore")  
Restoring the image should look similar to this:  
![alt text](images/Install-03.png "Restoring Image")  
Congratulations, Raspbian is now setup on your Raspberry Pi.


## Setting Up Networking on Raspberry Pi
We will be creating a headless Raspberry Pi (no ethernet cable needed)!  
In /etc/network/interface:  
```bash
auto lo
iface lo inet loopback

iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
```

In /etc/wpa_supplicant/wpa_supplicant.conf:  
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
        ssid="My Network"
        psk="Password"
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=CCMP
        auth_alg=OPEN
        scan_ssid=1
}
```

In /etc/hostname:  
```
robotpi
```

In /etc/hosts, last line should be:  
```
127.0.1.1	robotpi
```


## Wiring your robot
Attach the robot's left eye PWM to port 17 and right eye PWM to port 18 using pinout [here](https://www.raspberrypi.org/documentation/usage/gpio-plus-and-raspi2/).


## Connecting to Your Raspberry Pi
Find the local IP address of your raspberry pi through your WiFi Router (most likely looks something like `192.168.x.x`).  
If you are unable to find your Raspberry Pi's local IP address, then you can set the Raspberry Pi to have a fixed IP address (tutorials of how to do this can be found online).  
Run `ssh pi@192.168.x.x` and use 'raspberry' for a password.


## Get Your Robot Moving!
Once you are connected to your Raspberry Pi through ssh we need to update the software.  
First, let's expand the main partition. Do so by typing `sudo raspi-config` and choosing the 'Expand Filesystem' option.  
Now let's clone the git repository using the command `git clone https://github.com/Zackory/Pypr-Robots.git`.  
At this point you can automate the entire setup process by running `./setup.sh`.  
Once the Raspberry Pi is setup, run `sudo python ~/Pypr-Robots/robot-humanoid/humanoid.py` to launch the joystick controller for the humanoid robot.

If you would like to manually set up the Raspberry Pi, then continue reading.
Next, run `sudo apt-get update` followed by `sudo apt-get upgrade`. Lastly, reboot your Pi `sudo reboot`.  
Next we need to install 'pigpio' for controlling the servos through python.  
You can install pigpio using the official instructions [here](http://abyz.co.uk/rpi/pigpio/download.html).
or by using the terminal commands copied below for convenience.  
```
rm pigpio.zip
sudo rm -rf PIGPIO
wget abyz.co.uk/rpi/pigpio/pigpio.zip
unzip pigpio.zip
cd PIGPIO
make -j4
sudo make install
```
We then need to make sure pigpio is started each time the Raspberry Pi starts up.  
Run `sudo nano /etc/rc.local` and add `pigpiod` at the end of the file right before the line `exit 0`.  
Next we need to install 'pygame' so that we can access joystick controllers using python.  
To install pygame you can use `sudo apt-get install pygame`.  
Let's reboot once more `sudo reboot`, then we are good to go!  

If you have not already done so, run `sudo python ~/Pypr-Robots/robot-humanoid/humanoid.py` to launch the joystick controller for the humanoid robot.
