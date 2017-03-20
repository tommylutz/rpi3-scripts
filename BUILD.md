# Details of Canonical Machine Build

## Format SD card and load NOOBS
  * Download and installer SD card formatter https://www.sdcard.org/downloads/formatter_4/
  * Format FAT32 with size adjustment ON (options)
## Put NOOBS on the card
  * Download the offline installer from https://www.raspberrypi.org/downloads/noobs/
  * Put contents of that zip file into the root of the freshly formatted SD card
## Install Raspbian and set up Wi-Fi/SSH
  * Boot the Pi with a keyboard, mouse, and monitor connected to install Raspbian
  * Use the wifi icon in the desktop to configure a wifi connection
  * Run ```sudo raspi-config``` in a terminal
    * Enable SSH in interface options
    * Change default password (default is raspberry)
    * Change Locale to US/EN
    * Reboot when prompted
  * Upgrade packages to latest: ```sudo apt-get update; sudo apt-get upgrade```
## Localize Rpi3 to US
 * http://rohankapoor.com/2012/04/americanizing-the-raspberry-pi/
## Clone and Build quake3
```
sudo apt-get install libsdl1.2-dev
git clone https://github.com/tommylutz/quake3.git
cd quake3
./build_rpi_raspbian.sh
```
* Copy pak0.pk3 etc over to ```/home/pi/quake3/build/release-linux-arm/baseq3/``` using WinSCP
* Create ```/home/pi/Desktop/quake3.desktop``` shortcut file
* Test quake3 client by launching the icon on the desktop - works
* Test quake3 server by running ```/home/pi/quake3/start_server.sh```. Able to log into game from local client.
## Citrix Receiver
* Download the "Receiver for Linux (ARM HF)" debian package from https://www.citrix.com/downloads/citrix-receiver/linux/receiver-for-linux-latest.html
* ```sudo apt-get install libxerces-c3.1 libwebkitgtk-1.0-0``` (dependencies)
* ```sudo apt-get -f install``` (fix missing deps)
* ```sudo dpkg -i icaclient_13.5.0.10185126_armhf.deb```
* Link certs for ICA client from Mozilla ```sudo ln /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts/```
* Test ICA client
## EmulationStation / RetroPie
* Follow directions https://github.com/retropie/RetroPie-Setup/wiki/Manual-Installation
* Add ScummVM
* Fix failed boot into X ```sudo mv /etc/splashscreen.list /etc/splashscreen.list.sav```
* Reboot and validate X comes up and auto logs in
* Hit Ctrl+F1 and run ```emulationstation```. Verify it loads.
## Add wiimote support for emulationstation
* Install packages ```sudo apt-get install python-cwiid wminput```
* Add udev rules in ```/etc/udev/rules.d/wiimote.rules``` : ```KERNEL=="uinput", MODE="0666"```
* Add ```uinput``` to ```/etc/modules```
* Install files ```/home/pi/wiimotes.sh /home/pi/mywminput1```
* Test wiimote binding and map with emulationstation (1+2 on wiimote, run wiimotes.sh)
## OpenRCT2
* ```wget https://nih.at/libzip/libzip-1.2.0.tar.gz```, ./configure, sudo checkinstall (to install .deb)
* ```git clone https://github.com/OpenRCT2/OpenRCT2.git``` - standard cmake build, resolve deps

