---
layout: post
title: How to run an unattended Ubuntu install
date:   2017-12-29 00:00:00 +0200
categories: [linux]
---

# How to run an unattended Ubuntu install

- Download the base ISO: http://releases.ubuntu.com/17.04/ubuntu-17.04-server-amd64.iso
- Edit the ISO:
  - Add a auto-install.preseed file in preseed folder: https://gist.github.com/landier/2062aeabbb11a7eb6b30826578f9adce
  - Edit isolinux/txt.cfg and the following entry:

```shell
default autoinstall

label autoinstall
  menu label ^Auto Install Ubuntu Server
  kernel /install/vmlinuz
  append  file=/cdrom/preseed/auto-install.seed auto=true priority=critical debian-installer/locale=en_US keyboard-configuration/layoutcode=us languagechooser/language-name=English countrychooser/shortlist=FR localechooser/supported-locales=en_US.UTF-8 initrd=/install/initrd.gz quiet splash noprompt noshell ---
```

- Create the ISO:
```shell
mkisofs -D -r -V ATTENDLESS_UBUNTU -cache-inodes -J -l -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o ../autoinstall.iso .
```

