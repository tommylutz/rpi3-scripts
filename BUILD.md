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
## Clone and Build quake3
```
sudo apt-get install libsdl1.2-dev
git clone https://github.com/tommylutz/quake3.git
cd quake3
./build_rpi_raspbian.sh
```
* Copy pak0.pk3 etc over to ```/home/pi/quake3/build/release-linux-arm/baseq3/``` using WinSCP
* Create /home/pi/Desktop/quake3.desktop shortcut file
