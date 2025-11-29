# RPI_Signalk_Install

This repos is intended to give you the command lines and script to configure a Raspberry Pi with SignalK and and WaveShare 2Ch Can Hat.
Following the Youtube video explaining how I came to this configuration https://www.youtube.com/watch?v=LtRXSjpMPuI, I create a second video to describe how to install the configuration here 

# Command used during the configuration
get the bcm2835 source library and build it in the current folder
```
wget http://www.airspayce.com/mikem/bcm2835/bcm2835-1.60.tar.gz
tar zxvf bcm2835-1.60.tar.gz 
cd bcm2835-1.60/
sudo ./configure
sudo make
sudo make check
sudo make install
```

Add WiringPi library for access to the Waveshare board
```
wget https://files.waveshare.com/upload/8/8c/WiringPi-master.zip
sudo apt-get install unzip
unzip WiringPi-master.zip
cd WiringPi-master/
sudo ./build 
```

Add access to the Waveshare board into the __*/boot/firmware/config.txt*__ file (use text editor with super user right e.g. *sudo nano*) with these line
The access to the 2 chanels will be via __*can0*__ and __*can1*__
```
dtparam=spi=on
dtoverlay=mcp2515-can1,oscillator=16000000,interrupt=25
dtoverlay=mcp2515-can0,oscillator=16000000,interrupt=23
dtoverlay=spi-bcm2835-overlay
```

After a reboot, you can create the netwok links and configure the CAN port to 250kbit/s the NMEA2000 speed
```
sudo ip link set can0 up type can bitrate 250000
sudo ip link set can1 up type can bitrate 250000
sudo ifconfig can0 txqueuelen 65536
sudo ifconfig can1 txqueuelen 65536
```

Install the can util software to test the confuration
```
sudo apt-get install can-utils -y
```

Open the following 2 commands in 2 different terminal windows
```
candump can0
cansend can1 000#11.22.33.44
```

#SignalK installation
I follow the SignalK setup procedure
Install NodeJS, the necessary libraries and finally SignalK from NodeJS
```
sudo apt install nodejs
sudo apt install libnss-mdns avahi-utils libavahi-compat-libdnssd-dev
sudo npm install -g signalk-server
```
