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
