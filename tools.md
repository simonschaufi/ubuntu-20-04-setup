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
sudo apt install -y rbenv
rbenv init
printf "\neval \"\$(rbenv init -)\"\n" | tee -a ~/.bashrc

rbenv install 2.4.1
```

```bash
# As an rbenv plugin
mkdir -p "$(rbenv root)"/plugins
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```

verify installation

```bash
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/main/bin/rbenv-doctor | bash
```
