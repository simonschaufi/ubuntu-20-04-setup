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

```bash
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
cd ~/.rbenv && src/configure && make -C src
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
~/.rbenv/bin/rbenv init
echo 'eval "$(rbenv init -)"' >> ~/.bashrc

# Install ruby-build
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build

# verify installation
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/main/bin/rbenv-doctor | bash

# Download and install
rbenv install 3.0.2

# Set global version
rbenv global 3.0.2
```
