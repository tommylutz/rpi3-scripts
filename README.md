# rpi3-scripts
This repo contains Tom Lutz's personal notes and scripts for configuring a new Raspberry Pi 3 Model B with some applications, based on the default raspbian installation.

## Quake 3 Arena
* ioquake3 via github.com/tommylutz/quake3
```
#To start u automatically, add the following line to /etc/rc.local
su - pi -c "cd /home/pi/quake/quake3 && nohup /home/pi/quake/quake3/start_server.sh &"
```

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

Edit ```/etc/network/interfaces``` to set ```wlan0``` to static config:
```
allow-hotplug wlan0
iface wlan0 inet static
    address 192.168.2.1
    netmask 255.255.255.0
    network 192.168.2.0
    broadcast 192.168.2.255
```
You may wish to comment out the existing lines in case you wish to act as a wifi client again.

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

```
#Add the following to /etc/rc.local to auto-enable ip forwarding on startup:
iptables-restore < /etc/iptables.ipv4.nat
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## Citrix Receiver
* Include certificate installation
1. Download the "Receiver for Linux (ARM HF)" debian package from https://www.citrix.com/downloads/citrix-receiver/linux/receiver-for-linux-latest.html (wget may not work, as you have to accept the license)
```
wget https://downloads.citrix.com/12939/icaclient_13.5.0.10185126_armhf.deb?__gda__=1489922811_35d02cb8c45ec8bf003218ef82d5d6dc
```

## RetroPie
* https://github.com/retropie/RetroPie-Setup/wiki/Manual-Installation
```
sudo apt-get update
sudo apt-get upgrade
```
