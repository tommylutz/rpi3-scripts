# rpi3-scripts
This repo contains Tom Lutz's personal notes and scripts for configuring a new Raspberry Pi 3 Model B with some applications, based on the default raspbian installation.

### References
This is not all original material. I leveraged tutorials from the following sites:
* https://frillip.com/using-your-raspberry-pi-3-as-a-wifi-access-point-with-hostapd/


## Quake 3 Arena
* ioquake3 via github.com/tommylutz/quake3 (has minor fixes to link line and build script)
```
sudo apt-get install libsdl1.2-dev
git clone https://github.com/tommylutz/quake3.git
cd quake3
./build_rpi_raspbian.sh
```
Find ```pak0.pk3, pak1.pk3, ... pak8.pk3``` files from a legit copy of Quake 3 Arena, and copy to ```build/release-linux-arm/baseq3/```
```
./start_server.sh
```
The start_server.sh script will emit instructions on what to add to ```/etc/rc.local``` to start up quake 3 dedicated server on boot, if you'd like.

## Wifi Hotspot with NAT Routing

### hostapd
The hostapd package is required to turn RPi3 into a wifi hotspot.
After completing these steps, you
should be able to see the SSID of your Raspberry Pi broadcasting (Pi3-AP
unless you changed it). You will not get an IP address until you
configure ```dnsmasq``` (later).

First, install hostapd using aptget. 
```
sudo apt-get install dnsmasq hostapd
```
Next, create the hostapd config file, ```/etc/hostapd/hostapd.conf```
```
# This is the name of the WiFi interface we configured above
interface=wlan0

# This is the name of the network
ssid=Pi3-AP

# Use the 2.4GHz band
hw_mode=g

# Use channel 6
channel=6

# Enable 802.11n
ieee80211n=1

# Enable WMM
wmm_enabled=1

# Enable 40MHz channels with 20ns guard interval
ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]

# Accept all MAC addresses
macaddr_acl=0

# Use WPA authentication
auth_algs=1

# Require clients to know the network name
ignore_broadcast_ssid=0

# Use WPA2
wpa=2

# Use a pre-shared key
wpa_key_mgmt=WPA-PSK

# The network passphrase
wpa_passphrase=raspberry

# Use AES, instead of TKIP
rsn_pairwise=CCMP

```

Tell hostapd where the config file is by editing ```/etc/default/hostapd```
```
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

Edit ```/etc/network/interfaces``` to set ```wlan0``` to static ip address:
```
allow-hotplug wlan0
iface wlan0 inet static
    address 192.168.2.1
    netmask 255.255.255.0
    network 192.168.2.0
    broadcast 192.168.2.255
```

Restart your wlan0 interface and check that it has the correct IP address assigned.
```
sudo ifdown wlan0
sudo ifup wlan0
ifconfig wlan0
```

Start hostapd
```
sudo service hostapd start
```

Check that the access point is visible from a wifi client, such as your phone.


### dnsmasq

First, install the package
```
sudo apt-get install dnsmasq
```
Next, configure your DNS service in ```/etc/dnsmasq.conf```
This is the entire contents of the file. You may wish to save the original one to .sav or something.
```
interface=wlan0
listen-address=192.168.2.1
bind-interfaces
server=8.8.8.8
domain-needed
bogus-priv
dhcp-range=192.168.2.100,192.168.2.200,12h
dhcp-option=option:router,192.168.2.1
```

Fire up the service (yes, it will auto start on next boot)
```
sudo service dnsmasq start
```

### IP Routing / NAT
Set up IP routing rules
```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT  
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT  
```
Add this line to ```/etc/rc.local```
```
echo 1 > /proc/sys/net/ipv4/ip_forward
```
Also run this so it takes effect immediately
```
sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
```

Configure iptables persistently. First, export iptables config to a file:
```
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```
Now add this to ```/etc/rc.local``` to load the config on boot:
```
iptables-restore < /etc/iptables.ipv4.nat
```


## Citrix Receiver
* Include certificate installation
1. Download the "Receiver for Linux (ARM HF)" debian package from https://www.citrix.com/downloads/citrix-receiver/linux/receiver-for-linux-latest.html (wget may not work, as you have to accept the license)
```
wget https://downloads.citrix.com/12939/icaclient_13.5.0.10185126_armhf.deb?__gda__=1489922811_35d02cb8c45ec8bf003218ef82d5d6dc
```
2. Try to install the package, but fail miserably due to missing dependencies. ```sudo apt-cache search <SEARCH TERM>``` to search for the appropriate packages and ```sudo apt-get install <PACKAGES>``` to install them
3. Symlink root certs from mozilla into the Citrix folder.
```
sudo ln /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts/
```

## RetroPie
* https://github.com/retropie/RetroPie-Setup/wiki/Manual-Installation
```
sudo apt-get update
sudo apt-get upgrade
```
* Wiimotes: http://www.instructables.com/id/Wiimote-Controller-Configuration-for-Raspberry-Pi-/?ALLSTEPS
```
sudo apt-get install python-cwiid
sudo apt-get install wminput
```
File ```/etc/udev/rules.d/wiimote.rules```
```
KERNEL=="uinput", MODE="0666"
```
Check bluetooth
```
/etc/init.d/bluetooth status
```
File ```/home/pi/mywinput```
```
#WiiMote
Wiimote.A  = BTN_A
Wiimote.B = BTN_B
Wiimote.Dpad.X = ABS_Y
Wiimote.Dpad.Y = -ABS_X
Wiimote.Minus = BTN_SELECT
Wiimote.Plus = BTN_START
Wiimote.Home = BTN_MODE
Wiimote.1 = BTN_X
Wiimote.2 = BTN_Y
# Nunchuk
Nunchuk.C = BTN_C
Nunchuk.Z = BTN_Z
Plugin.led.Led1 = 1
#Plugin.led.Led2 = 1
Plugin.led.Led3 = 1
#Plugin.led.Led4 = 1
```
