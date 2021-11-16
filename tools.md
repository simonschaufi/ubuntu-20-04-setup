# Tools

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
sudo apt install -y php-yaml php-mongodb
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
rbenv install 3.0.2

# Set global version
rbenv global 3.0.2

# Install ruby gems
gem install bundler
```

## Vagrant

https://www.vagrantup.com/downloads

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install -y vagrant
```


## Virtualbox

https://www.virtualbox.org/wiki/Linux_Downloads

```bash
sudo tee /etc/apt/sources.list.d/virtualbox.list <<END
deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian focal contrib
END

wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -

sudo apt update && sudo apt install -y virtualbox-6.1
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
