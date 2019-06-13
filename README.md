# Raspberry Pi Setup (Raspbian 8, Jessie)

## Wifi Setup

### WPA supplicant

Open `/etc/wpa_supplicant/wpa_supplicant.conf`

Example format:

```
update_config=1
country=US
# set location of the control socket (group: users that can use wpa_cli, or just run it as root)
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev

network={
	ssid=[wifi-network-name]
	psk=[encrypted-network-password] # encryption optional? otherwise, just the network password
	priority=[ranking-number] # higher priority => preferred connection
	id_str=[identifier-string] # can be anything
	key_mgmt=[wireless-security-protocol] # e.g. WPA-PSK
	password=[] # ???? might be possible to have a plaintext password, did not experiment with this
}
```

Generate the encrypted WPA password (psk) using `wpa_passphrase`, which will
output a network interface with a hashed psk.

```
wpa_passphrase [ssid-name] [password-name]
```

Run `iwgetid` to see what wifi network the Pi is connected to.

### Network Interfaces

Open `/etc/network/interfaces` and make sure the following lines are in there someplace:
```
auto wlan0
iface wlan0 inet manual
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```
or for wifi automatic reconnect 
```
allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

### (Optional) Install a WIFI manager

Raspberry Pi + WICD set-up guide:
https://blog.onetwentyseven001.com/easy-wireless-configuration-for-raspberry-pi/#.Wm_iGfaQytE

General WICD guide:
https://wiki.archlinux.org/index.php/wicd

## Connecting to the Raspberry Pi

Very useful documentation for a variety of ways to connect to the pi:
https://www.raspberrypi.org/documentation/remote-access/

## Finding the Raspberry Pi on the network

Find your router's ip address by opening up 'System Preferences'->'Network' on MacOS.
On Linux, run `hostname -I`.

Then run:
```
sudo nmap -sP [router-ip-addres]/24
```

You should see a name 'raspberrypi' listed next to an IP address. If not, be sure to sudo,
or take a look at the OUI of the MAC address. Raspberry Pi MAC addresses are prefixed with the OUI B8:27:EB.
The first three octets of the MAC address are the Organizationally Unique Identifier (OUI) for the Raspberry Pi Foundation.

If that doesn't find it / takes too long, check the arp tables:
```
arp -na | grep b8
```
