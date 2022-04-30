# Software

## Anydesk

http://deb.anydesk.com/howto.html

```bash
wget -qO - https://keys.anydesk.com/repos/DEB-GPG-KEY | sudo apt-key add -
sudo echo "deb http://deb.anydesk.com/ all main" > /etc/apt/sources.list.d/anydesk-stable.list
sudo apt update && sudo apt install -y anydesk
```

## Teamviewer

```bash
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
sudo apt install -y ./teamviewer_amd64.deb
rm teamviewer_amd64.deb
```

## Audacity

https://askubuntu.com/a/229365

```bash
sudo apt update && sudo apt install -y pavucontrol audacity
```

## Audio Recorder

https://askubuntu.com/a/298286

```bash
sudo add-apt-repository ppa:audio-recorder/ppa
sudo apt update -y && sudo apt install -y audio-recorder
```

## OBS Studio

```bash
sudo add-apt-repository -y ppa:obsproject/obs-studio
sudo apt update && sudo apt install -y ffmpeg obs-studio
```

## Clementine

```bash
sudo apt update && sudo apt install -y clementine
```

## DBeaver

```bash
wget -O - https://dbeaver.io/debs/dbeaver.gpg.key | sudo apt-key add -
echo "deb https://dbeaver.io/debs/dbeaver-ce /" | sudo tee /etc/apt/sources.list.d/dbeaver.list
sudo apt update && sudo apt install -y dbeaver-ce
```

## DB Browser for SQLite

```bash
sudo apt install -y sqlitebrowser
```

## Robo 3T

* https://robomongo.org/download
* https://gist.github.com/abdallahokasha/37911a64ad289487387e2d1a144604ae

```bash
wget https://download.studio3t.com/robomongo/linux/robo3t-1.4.4-linux-x86_64-e6ac9ec.tar.gz
tar xfz robo3t-1.4.4-linux-x86_64-e6ac9ec.tar.gz
rm robo3t-1.4.4-linux-x86_64-e6ac9ec.tar.gz

sudo mkdir /usr/local/bin/robo3t
sudo mv robo3t-1.4.4-linux-x86_64-e6ac9ec/* /usr/local/bin/robo3t/
rmdir robo3t-1.4.4-linux-x86_64-e6ac9ec
cd /usr/local/bin/robo3t/bin
wget https://dashboard.snapcraft.io/site_media/appmedia/2018/09/logo-256x256.png

sudo tee /usr/share/applications/robo3t.desktop <<EOL
[Desktop Entry]
Encoding=UTF-8
Type=Application
Name=Robo3t
Icon=/usr/local/bin/robo3t/bin/logo-256x256.png
Exec="/usr/local/bin/robo3t/bin/robo3t"
Comment=Robo3t
Categories=Development;
Terminal=false
StartupNotify=true
EOL
```

## Double commander

https://doublecmd.sourceforge.io/

```bash
echo 'deb http://download.opensuse.org/repositories/home:/Alexx2000/xUbuntu_20.04/ /' | sudo tee /etc/apt/sources.list.d/home:Alexx2000.list
curl -fsSL https://download.opensuse.org/repositories/home:Alexx2000/xUbuntu_20.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/home_Alexx2000.gpg > /dev/null
sudo apt update && sudo apt install -y doublecmd-gtk
```

## PDF Arranger

https://wiki.ubuntuusers.de/PDF_Arranger/

```bash
sudo apt install -y pdfarranger
```

## OwnCloud

```bash
wget -nv https://download.owncloud.com/desktop/ownCloud/stable/latest/linux/Ubuntu_20.04/Release.key -O - | sudo apt-key add -
echo 'deb https://download.owncloud.com/desktop/ownCloud/stable/latest/linux/Ubuntu_20.04/ /' | sudo tee -a /etc/apt/sources.list.d/owncloud.list
sudo apt update && sudo apt install owncloud-client
```

## Keybase

```bash
curl --remote-name https://prerelease.keybase.io/keybase_amd64.deb
sudo apt install -y ./keybase_amd64.deb
run_keybase
rm keybase_amd64.deb
```

## Signal Desktop

https://signal.org/de/download/

```bash
# NOTE: These instructions only work for 64 bit Debian-based
# Linux distributions such as Ubuntu, Mint etc.

# 1. Install our official public software signing key
wget -O- https://updates.signal.org/desktop/apt/keys.asc | gpg --dearmor > signal-desktop-keyring.gpg
cat signal-desktop-keyring.gpg | sudo tee -a /usr/share/keyrings/signal-desktop-keyring.gpg > /dev/null

# 2. Add our repository to your list of repositories
echo 'deb [arch=amd64 signed-by=/usr/share/keyrings/signal-desktop-keyring.gpg] https://updates.signal.org/desktop/apt xenial main' |\
  sudo tee -a /etc/apt/sources.list.d/signal-xenial.list

# 3. Update your package database and install signal
sudo apt update && sudo apt install signal-desktop
```

## KeepassXC

```bash
sudo add-apt-repository -y ppa:phoerious/keepassxc
sudo apt update && sudo apt install -y keepassxc
```

## Discord

```bash
wget https://dl.discordapp.net/apps/linux/0.0.10/discord-0.0.10.deb -O discord.deb
sudo apt install -y ./discord.deb
rm discord.deb
```

## Mattermost

https://github.com/mattermost/desktop/releases

```bash
sudo apt install -y gconf-service gconf-service-backend gconf2 gconf2-common libgconf-2-4
wget https://github.com/mattermost/desktop/releases/download/v5.0.2/mattermost-desktop-5.0.2-linux-amd64.deb
sudo dpkg -i mattermost-desktop-5.0.2-linux-amd64.deb && rm mattermost-desktop-5.0.2-linux-amd64.deb
```

## Slack

```bash
wget https://downloads.slack-edge.com/releases/linux/4.21.1/prod/x64/slack-desktop-4.21.1-amd64.deb
sudo apt install -y ./slack-desktop-4.21.1-amd64.deb && rm slack-desktop-4.21.1-amd64.deb
```

## Skype

```bash
wget https://go.skype.com/skypeforlinux-64.deb
sudo apt install -y ./skypeforlinux-64.deb && rm skypeforlinux-64.deb
```

## Teams

```bash
wget 'https://teams.microsoft.com/downloads/desktopurl?env=production&plat=linux&arch=x64&download=true&linuxArchiveType=deb' -O teams_amd64.deb
sudo apt install -y ./teams_amd64.deb && rm teams_amd64.deb
```

## Zoom

```bash
wget https://zoom.us/client/latest/zoom_amd64.deb
sudo apt install -y ./zoom_amd64.deb && rm zoom_amd64.deb
```
