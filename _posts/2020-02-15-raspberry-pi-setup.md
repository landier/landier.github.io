---
layout: post
title: How to make Rasperry Pi boot on USB
date:   2020-02-15 00:00:00 +0200
categories: [linux, raspberry]
---

_Goal of this post is to sum up the the steps to end up with a USB booted Raspberry Pi on Fedora - all prepared on Mac OS._

# Prepare SD card
* Raspbian SD card

# Prepare USB pendrive with Fedora
Format USB pendrive then:
```
cd /tmp
curl -o Fedora-Minimal-31-1.9.aarch64.raw.xz https://dl.fedoraproject.org/pub/fedora-secondary/releases/31/Spins/aarch64/images/Fedora-Minimal-31-1.9.aarch64.raw.xz
unxz Fedora-Minimal-31-1.9.aarch64.raw.xz
sudo diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=Fedora-Minimal-31-1.9.aarch64.raw of=/dev/rdisk2 conv=syn
```

# First boot on Raspbian SD card
```
echo program_usb_boot_mode=1 | sudo tee -a /boot/config.txt
sudo reboot
```
Then check you get _17:3020000a_ output by the following command:
```
vcgencmd otp_dump | grep 17:
```

# USB boot
You now can plug the USB pendrive and remove the SD card, then reboot and proceed with Fedora install.

# Resize disk
Once you're done, do not forget to expand root partition on USB pendrive:
```
growpart /dev/sda 3
resize2fs /dev/sda3
```

Sources:
* https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md
* https://fedoraproject.org/wiki/Architectures/ARM/Raspberry_Pi
