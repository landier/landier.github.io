---
layout: post
title: How to make Rasperry Pi boot on USB
date:   2020-02-15 00:00:00 +0200
categories: [linux, raspberry]
---

_Goal of this post is to sum up the the steps to end up with a USB booted Raspberry Pi on Fedora - all prepared on Mac OS._

# Prepare SD card
```
curl -L -o raspbian-lite.img.zip https://downloads.raspberrypi.org/raspbian_lite_latest
unzip raspbian-lite.img.zip
sudo diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=raspbian-lite.img of=/dev/rdisk2 conv=syn

```

# Prepare USB pendrive with Fedora
Format USB pendrive then:
```
cd /tmp
curl -o Fedora-Minimal-armhfp-31-1.9-sda.raw.xz https://download.fedoraproject.org/pub/fedora/linux/releases/31/Spins/armhfp/images/Fedora-Minimal-armhfp-31-1.9-sda.raw.xz
unxz Fedora-Minimal-armhfp-31-1.9-sda.raw.xz
sudo diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=Fedora-Minimal-armhfp-31-1.9-sda.raw of=/dev/rdisk2 conv=syn
```
Edit: I switched to the armv7 version because the aarch64 kernel has a tendency for a high load (with low CPU usage). Looks like this issue https://github.com/raspberrypi/linux/issues/2881 which has been solved with kernel 5.0.0 on arvm7.

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
sudo su
growpart /dev/sda 3
resize2fs /dev/sda3
```

# Sources
* https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/msd.md
* https://fedoraproject.org/wiki/Architectures/ARM/Raspberry_Pi
