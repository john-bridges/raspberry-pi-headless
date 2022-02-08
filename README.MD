# Raspberry Pi Headless - Wireless Only

Notes on configuring new Ubuntu Server or Raspberry Pi OS Lite images for wireless only connectivity, WPA2-Personal no initial ethernet connection is necessary. Tested on Raspberry Pi Zero 2W but should work for any Raspberry Pi model.

#### Raspberry PI OS Lite (32 Bit Specifically Pi Zero 2W)
Mount the **boot** volume on the micro sd card and create an empty "ssh" file in the root of that volume. Next create a **wpa_supplicant.conf** file in the root of the boot volume with the following config:
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
country=US
update_config=1
network={
 	ssid="Name of your wireless LAN"
 	psk="Password for your wireless LAN"
}
```

#### Ubuntu Server 21.10 (32 Bit Specifically Pi Zero 2W)
Mount the **system-boot** and **writable** volumes on your micro sd card. 

#### system-boot volume file changes/additions
Locate the **network-config** file in the root of the system-boot volume, edit your file filling in the SSID and password, taking care to preserve the indentation in the yaml file.
```
version: 2
wifis:
  wlan0:
    dhcp4: true
    optional: true
    access-points:
      "yourSSID":
        password: "yourPswd"
```
Add an empty **ssh** file
Locate the **user-data** file, at the bottom of the file add the following yaml config:
```
power_state:
  mode: reboot
 ```
 this will reboot the Pi when cloud-init completes

#### writable volume file changes/additions
Locate the **wpa_supplicant** folder, it should be located in **\etc** folder. Inside that folder create a **wpa_supplicant.conf** file and add the following config:
```
network={
 	ssid="Name of your wireless LAN"
 	psk="Password for your wireless LAN"
}
```

Assigned mac address range for pi's depends on models
```
e4:5f:01:xx:xx:xx
b8:27:eb:xx:xx:xx
dc:a6:32:xx:xx:xx
```

Finding the pi on the network, arp does not always work, nmap can be used in the case when it doesn't work.  
- arp -na | grep -i "e4:5f:01"
- nmap -sP 192.168.1.0/24
