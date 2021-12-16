# Tools

## Install additional languages with aspell

This is useful for german dictionary in PhpStorm. See https://stackoverflow.com/questions/1949976/where-to-find-dictionaries-for-other-languages-for-intellij

```bash
sudo apt install aspell-de

# Generate language file for phpstorm:
aspell --lang=de dump master | aspell --lang=de expand | tr ' ' '\n' > de.dic
```

## Curl

```bash
sudo apt install curl
```

## Install additional 7zip packages

```bash
sudo apt install -y p7zip p7zip-full p7zip-rar
```

## Install additional php extensions

```bash
sudo apt install -y php-yaml php7.4-mongodb php8.0-mongodb
```

## Install image minifier

* https://github.com/svg/svgo

```bash
sudo apt install -y optipng jpegoptim libjpeg-turbo-progs gifsicle
yarn global add svgo
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
