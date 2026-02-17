# Tools

## GitHub cli

https://github.com/cli/cli/blob/trunk/docs/install_linux.md

```bash
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```


## Install additional languages with aspell

This is useful for german dictionary in PhpStorm. See https://stackoverflow.com/questions/1949976/where-to-find-dictionaries-for-other-languages-for-intellij

```bash
sudo apt install aspell-de

# Generate language file for phpstorm:
aspell --lang=de dump master | aspell --lang=de expand | tr ' ' '\n' > de.dic
```

## Curl

```bash
sudo apt install -y curl
```

## jq

commandline JSON processor 

```bash
sudo apt install -y jq
```

## parallel

This is needed to run unit tests in parallel

```bash
sudo apt install -y parallel
```

## ack

```bash
sudo apt install -y ack
```

How to use it?

```bash
ack 'use ProphecyTrait' typo3/|wc -l; ack 'prophesize' typo3/|wc -l
```

## Install additional 7zip packages

```bash
sudo apt install -y p7zip p7zip-full p7zip-rar
```

## Graphicsmagick

```bash
sudo apt install -y graphicsmagick
```

## Install additional php extensions

* php-vips is required for neos.io

```bash
sudo apt install -y php-yaml php7.4-mongodb php8.0-mongodb \
  php7.0-vips php7.1-vips php7.2-vips php7.3-vips php7.4-vips php8.0-vips php8.1-vips
```

## Install image minifier

* https://github.com/svg/svgo

```bash
sudo apt install -y optipng jpegoptim libjpeg-turbo-progs gifsicle
yarn global add svgo
```

## SVN

```bash
sudo apt install -y subversion
```

## Install Ruby with rbenv

* https://github.com/rbenv/rbenv#installation
* https://github.com/rbenv/ruby-build#installation
* https://github.com/tpope/rbenv-aliases

```bash
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
cd ~/.rbenv && src/configure && make -C src
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
~/.rbenv/bin/rbenv init
echo 'eval "$(rbenv init -)"' >> ~/.bashrc

# Install ruby-build plugin
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build

# Install aliases plugin
mkdir -p "$(rbenv root)/plugins"
git clone https://github.com/tpope/rbenv-aliases.git "$(rbenv root)"/plugins/rbenv-aliases
rbenv alias --auto

# verify installation
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/main/bin/rbenv-doctor | bash

# Download and install
rbenv install 2.7.4
rbenv install 3.0.2

# Set global version
rbenv global 2.7.4

# Install ruby gems
gem install bundler
```

## Vagrant

https://www.vagrantup.com/downloads

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install -y vagrant

vagrant plugin install vagrant-hostsupdater
```

## Virtualbox

https://www.virtualbox.org/wiki/Linux_Downloads

* Version 6.1.28 is buggy!

```bash
wget -q https://download.virtualbox.org/virtualbox/6.1.26/virtualbox-6.1_6.1.26-145957~Ubuntu~eoan_amd64.deb
sudo apt install -y virtualbox-6.1_6.1.26-145957~Ubuntu~eoan_amd64.deb && rm virtualbox-6.1_6.1.26-145957~Ubuntu~eoan_amd64.deb
```

## Mkcert

https://github.com/FiloSottile/mkcert#linux

```bash
sudo apt install -y libnss3-tools
cd ~/Downloads
wget https://github.com/FiloSottile/mkcert/releases/download/v1.4.3/mkcert-v1.4.3-linux-amd64
sudo mv mkcert-v1.4.3-linux-amd64 /usr/local/bin/mkcert
sudo chmod +x /usr/local/bin/mkcert
```

## Chef

```
wget http://downloads.cinc.sh/files/unstable/cinc-workstation/21.11.679/ubuntu/20.04/cinc-workstation_21.11.679-1_amd64.deb
sudo apt install -y ./cinc-workstation_21.11.679-1_amd64.deb && rm cinc-workstation_21.11.679-1_amd64.deb
```

## Mongodb

https://docs.mongodb.com/v4.0/tutorial/install-mongodb-on-ubuntu/ (slightly modified as the original doesn't support arm64 !)

```bash
wget -qO - https://www.mongodb.org/static/pgp/server-4.0.asc | sudo apt-key add -
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
sudo apt update && sudo apt install -y mongodb-org

# Enable and start MongoDB Service
sudo systemctl enable mongod.service
sudo systemctl start mongod.service

sudo apt install -y --allow-change-held-packages \
  php-mongodb
```

## .NET SDK

https://docs.microsoft.com/de-de/dotnet/core/install/linux-ubuntu#2004-

```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb

sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-6.0

# Opt-out of .NET telemetry
tee -a ~/.profile <<EOL

# Opt-out of .NET telemetry
DOTNET_CLI_TELEMETRY_OPTOUT=1
EOL
```

## Flutter

https://docs.flutter.dev/get-started/install/linux

```bash
cd ~/Downloads/
wget https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.13.9-stable.tar.xz
tar xf ~/Downloads/flutter_linux_3.13.9-stable.tar.xz
echo 'export PATH="$PATH:$HOME/Downloads/flutter/bin"' >> $HOME/.bashrc
source $HOME/.bashrc
flutter config --no-analytics
flutter --disable-telemetry
flutter precache
```

## Dart

https://dart.dev/get-dart

```bash
sudo apt-get update
sudo apt install -y apt-transport-https
wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo gpg --dearmor -o /usr/share/keyrings/dart.gpg
echo 'deb [signed-by=/usr/share/keyrings/dart.gpg arch=amd64] https://storage.googleapis.com/download.dartlang.org/linux/debian stable main' | sudo tee /etc/apt/sources.list.d/dart_stable.list
sudo apt-get update
sudo apt install -y dart

export PATH="$PATH:/usr/lib/dart/bin"
echo 'export PATH="$PATH:/usr/lib/dart/bin"' >> $HOME/.profile
```

## Go

https://go.dev/doc/install

```bash
wget https://go.dev/dl/go1.26.0.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.26.0.linux-amd64.tar.gz
export PATH="$PATH:/usr/local/go/bin"
echo 'export PATH="$PATH:/usr/local/go/bin"' >> $HOME/.profile
rm go1.26.0.linux-amd64.tar.gz
```

## Ubuntu cleaner

https://phoenixnap.com/kb/ubuntu-remove-old-kernels

```bash
sudo add-apt-repository -y ppa:gerardpuig/ppa
sudo apt install -y ubuntu-cleaner
```
