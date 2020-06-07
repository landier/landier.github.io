---
layout: post
title: Setup a new MacOS machine
date:   2020-05-12 00:00:00 +0200
categories: [linux, macos]
---

```bash
curl -s "https://get.sdkman.io" | bash

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

brew install ack \
             bitwarden-cli \
             docker-compose \
             git-review \
             gpg \
             openssl \
             yadm

# Powerline fonts
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
cd ..
rm -rf fonts

# Install cask apps
brew cask install aerial \
                  balenaetcher \
                  bitwarden \
                  brave-browser-beta \
                  dbeaver-community \
                  docker-edge \
                  dropbox \
                  firefox-beta \
                  google-chrome-beta \
                  hyper \
                  intellij-idea \
                  iterm2-beta \
                  keka \
                  keybase \
                  ledger-live \
                  microsoft-excel \
                  microsoft-powerpoint \
                  microsoft-word \
                  obinslab-starter \
                  onedrive \
                  p4v \
                  slack \
                  sourcetree \
                  spotify \
                  visual-studio-code-insiders \
                  vlc \
                  zoomus
```
