---
layout: post
title: How to setup Lakka on Raspberry Pi
date:   2020-03-23 00:00:00 +0200
categories: [linux, raspberry]
---

Here is a walkthrough to quickly have [Lakka](http://www.lakka.tv/) up and running on a Raspberry Pi.

# Flash Lakka on a USB drive
Since I prefer using USB drive than micro-SD card on my Raspberry Pi here how it goes after your plugged your USB drive in:
```
cd /tmp
curl -o Lakka-RPi2.arm-2.3.2.img.gz http://le.builds.lakka.tv/RPi2.arm/Lakka-RPi2.arm-2.3.2.img.gz
diskutil unmountDisk /dev/sda
sudo dd if=Lakka-RPi2.arm-2.3.2.img of=/dev/rdisk2 && sync
```

The small but important particularity with the USB drive way is to edit the bootloader configuration which is the cmdline.txt file for Raspberry Pi. For that simply edit the cmdline.txt file on your USB drive from:
```
boot=LABEL=LAKKA disk=LABEL=LAKKA_DISK BOOT_IMAGE=/kernel.img quiet  vt.global_cursor_default=0 loglevel=2
```

to:
```
boot=/dev/sda1 disk=/dev/sda2 quiet  vt.global_cursor_default=0 loglevel=2
```

Once done, you're good to boot your Raspberry Pi using the USB drive.

# Enable services: SSH, Samba and Bluetooth
Once started, first thing to do on the GUI is to enable these three services via the menu Settings > Services:
* SSH
* SAMBA
* Bluetooth

# Connect to Wi-Fi
Connect the Pi to the Wi-Fi network via the GUI.

# Transfer your ROMs
Via Samba (or scp), just transfer all your beloved ROMs into /storage/roms folder.
Via the GUI make sure to trigger a scan of the /storage/roms directory.

# Pair your Bluetooh controller
Over SSH, do pair the Bluetooth controller by starting the Bluetooth CLI:
```
bluetoothctl
```

and issue following commands:
```
agent on
default-agent
power on
discoverable on
pairable on
scan on
```

Then enable the pairing on the controller to identify the MAC address and:
```
connect <MAC address>
trust <MAC address>
```

# Set the timezone
```
echo "TIMEZONE=Europe/Paris" > /storage/.cache/timezone
```

Sources:
* https://forums.libretro.com/t/raspberry-pi-3-usb-boot/12172/14
* [Pairing 8Bitdo controller over Bluetooth](https://www.lakka.tv/doc/8Bitdo-Wireless-Controller/)
* [Timezone settings](https://www.lakka.tv/doc/Timezone-settings/)
