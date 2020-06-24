---
layout: post
title: First minutes on a new machine
date:   2020-03-22 00:00:00 +0200
categories: [linux, fedora, ansible]
---

__Work in Progress__

# Firefox
1. Add password manager extension
2. Log into Firefox and let the sync happen

# Fedora specific - disable LVM2 monitoring service
Due to a bug in Fedora 31 preventing the system to boot, I tend to disable the lvm2-monitor service:
```
sudo systemctl mask lvm2-monitor2
```

# Execute Ansible playbook
Open a terminal and execute this:

```
# Install ansible
# Fedora: sudo dnf install -y ansible
# Ubuntu: sudo apt install -y ansible

# Clone and execute Playbook
git clone https://github.com/landier/playbooks
ansible-playbook local.yml -c localhost --ask-become-pass
```

# The old way

```bash
sudo dnf install fedora-workstation-repositories
sudo dnf config-manager --set-enabled google-chrome
sudo dnf install google-chrome-beta

sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
sudo dnf check-update
sudo dnf install code-insiders
```
