# Ubuntu 20.04 setup scripts

Execute sudo without Password

* https://askubuntu.com/a/147265

See https://github.com/laravel/settler/blob/9aeeefd19702dafdc32b4de0761a497fca3b609d/scripts/amd64.sh

Run this script with executing `sudo -s` before!

```bash
export DEBIAN_FRONTEND=noninteractive

USER="simon"

ARCH=$(arch)

SKIP_PHP=false
SKIP_MYSQL=false
SKIP_MARIADB=true
SKIP_POSTGRESQL=true

echo "### Settler Build Configuration ###"
echo "USER             = ${USER}"
echo "ARCH             = ${ARCH}"
echo "SKIP_PHP         = ${SKIP_PHP}"
echo "SKIP_MYSQL       = ${SKIP_MYSQL}"
echo "SKIP_MARIADB     = ${SKIP_MARIADB}"
echo "SKIP_POSTGRESQL  = ${SKIP_POSTGRESQL}"
echo "### Settler Build Configuration ###"

# Update Package List
apt-get update

# Update System Packages
apt-get upgrade -y

# Force Locale
echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
locale-gen en_US.UTF-8

apt-get install -y software-properties-common curl gnupg debian-keyring debian-archive-keyring apt-transport-https \
ca-certificates

# Install Some PPAs
apt-add-repository ppa:ondrej/php -y
apt-add-repository ppa:chris-lea/redis-server -y

# NodeJS
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -

# -- Skip Postgres part

## Update Package Lists
apt-get update -y

# Install Some Basic Packages
apt-get install -y build-essential dos2unix gcc git git-lfs libmcrypt4 libpcre3-dev libpng-dev chrony unzip make pv \
python3-pip re2c supervisor unattended-upgrades whois vim cifs-utils bash-completion zsh graphviz avahi-daemon tshark

# Set My Timezone
ln -sf /usr/share/zoneinfo/UTC /etc/localtime

# Install docker-ce
curl -fsSL https://get.docker.com | bash -s

# Enable user to run docker commands
usermod -aG docker ${USER}

# Install docker-compose
curl \
    -L "https://github.com/docker/compose/releases/download/1.26.0/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

if "$SKIP_PHP"; then
  echo "SKIP_PHP is being used, so we're not installing PHP"
else
  # Install Generic PHP packages
  apt-get install -y --allow-change-held-packages \
  php-imagick php-memcached php-redis php-xdebug php-dev php-swoole imagemagick mcrypt

  # PHP 7.0
  apt-get install -y --allow-change-held-packages \
  php7.0-bcmath php7.0-bz2 php7.0-cgi php7.0-cli php7.0-common php7.0-curl php7.0-dba php7.0-dev php7.0-enchant \
  php7.0-fpm php7.0-gd php7.0-gmp php7.0-imap php7.0-interbase php7.0-intl php7.0-json php7.0-ldap php7.0-mbstring \
  php7.0-mcrypt php7.0-mysql php7.0-odbc php7.0-opcache php7.0-pgsql php7.0-phpdbg php7.0-pspell php7.0-readline \
  php7.0-recode php7.0-snmp php7.0-soap php7.0-sqlite3 php7.0-sybase php7.0-tidy php7.0-xml php7.0-xmlrpc php7.0-xsl \
  php7.0-zip php7.0-imagick php7.0-memcached php7.0-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.0/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.0/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.0/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.0/cli/php.ini

  # Configure Xdebug
  echo "xdebug.remote_enable = 1" >> /etc/php/7.0/mods-available/xdebug.ini
  echo "xdebug.remote_connect_back = 1" >> /etc/php/7.0/mods-available/xdebug.ini
  echo "xdebug.remote_port = 9000" >> /etc/php/7.0/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/7.0/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/7.0/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.0/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.0/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.0/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/7.0/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/7.0/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.0/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.0/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.0/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.0/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/7.0/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.0/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/7.0/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.0/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/7.0/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/7.0/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/7.0/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/7.0/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.0/fpm/pool.d/www.conf

  # PHP 7.1
  apt-get install -y --allow-change-held-packages \
  php7.1-bcmath php7.1-bz2 php7.1-cgi php7.1-cli php7.1-common php7.1-curl php7.1-dba php7.1-dev php7.1-enchant \
  php7.1-fpm php7.1-gd php7.1-gmp php7.1-imap php7.1-interbase php7.1-intl php7.1-json php7.1-ldap php7.1-mbstring \
  php7.1-mcrypt php7.1-mysql php7.1-odbc php7.1-opcache php7.1-pgsql php7.1-phpdbg php7.1-pspell php7.1-readline \
  php7.1-recode php7.1-snmp php7.1-soap php7.1-sqlite3 php7.1-sybase php7.1-tidy php7.1-xdebug php7.1-xml php7.1-xmlrpc \
  php7.1-xsl php7.1-zip php7.1-imagick php7.1-memcached php7.1-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.1/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.1/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.1/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.1/cli/php.ini

  # Configure Xdebug
  echo "xdebug.remote_enable = 1" >> /etc/php/7.1/mods-available/xdebug.ini
  echo "xdebug.remote_connect_back = 1" >> /etc/php/7.1/mods-available/xdebug.ini
  echo "xdebug.remote_port = 9000" >> /etc/php/7.1/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/7.1/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/7.1/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.1/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.1/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.1/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/7.1/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/7.1/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.1/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.1/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.1/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.1/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/7.1/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.1/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/7.1/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.1/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/7.1/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/7.1/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/7.1/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/7.1/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.1/fpm/pool.d/www.conf

  # PHP 7.2
  apt-get install -y --allow-change-held-packages \
  php7.2-bcmath php7.2-bz2 php7.2-dba php7.2-enchant php7.2-fpm php7.2-imap php7.2-interbase php7.2-intl \
  php7.2-mbstring php7.2-phpdbg php7.2-soap php7.2-sybase php7.2-xsl php7.2-zip php7.2-cgi php7.2-cli php7.2-common \
  php7.2-curl php7.2-dev php7.2-gd php7.2-gmp php7.2-json php7.2-ldap php7.2-mysql php7.2-odbc php7.2-opcache \
  php7.2-pgsql php7.2-pspell php7.2-readline php7.2-recode php7.2-snmp php7.2-sqlite3 php7.2-tidy php7.2-xdebug \
  php7.2-xml php7.2-xmlrpc php7.2-imagick php7.2-memcached php7.2-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.2/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.2/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.2/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.2/cli/php.ini

  # Configure Xdebug
  echo "xdebug.mode = debug" >> /etc/php/7.2/mods-available/xdebug.ini
  echo "xdebug.discover_client_host = true" >> /etc/php/7.2/mods-available/xdebug.ini
  echo "xdebug.client_port = 9003" >> /etc/php/7.2/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/7.2/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/7.2/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.2/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.2/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.2/fpm/php.ini
  sed -i "s/; max_input_vars = .*/max_input_vars = 1500/" /etc/php/7.2/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/7.2/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.2/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.2/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.2/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.2/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/7.2/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.2/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/7.2/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.2/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/7.2/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/7.2/fpm/pool.d/www.conf

  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/7.2/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/7.2/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.2/fpm/pool.d/www.conf

  # PHP 7.3
  apt-get install -y --allow-change-held-packages \
  php7.3 php7.3-bcmath php7.3-bz2 php7.3-cgi php7.3-cli php7.3-common php7.3-curl php7.3-dba php7.3-dev php7.3-enchant \
  php7.3-fpm php7.3-gd php7.3-gmp php7.3-imap php7.3-interbase php7.3-intl php7.3-json php7.3-ldap php7.3-mbstring \
  php7.3-mysql php7.3-odbc php7.3-opcache php7.3-pgsql php7.3-phpdbg php7.3-pspell php7.3-readline php7.3-recode \
  php7.3-snmp php7.3-soap php7.3-sqlite3 php7.3-sybase php7.3-tidy php7.3-xdebug php7.3-xml php7.3-xmlrpc php7.3-xsl \
  php7.3-zip php7.3-imagick php7.3-memcached php7.3-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.3/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.3/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.3/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.3/cli/php.ini

  # Configure Xdebug
  echo "xdebug.mode = debug" >> /etc/php/7.3/mods-available/xdebug.ini
  echo "xdebug.discover_client_host = true" >> /etc/php/7.3/mods-available/xdebug.ini
  echo "xdebug.client_port = 9003" >> /etc/php/7.3/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/7.3/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/7.3/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.3/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.3/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.3/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/7.3/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/7.3/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.3/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.3/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.3/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.3/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/7.3/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.3/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/7.3/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.3/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/7.3/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/7.3/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/7.3/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/7.3/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.3/fpm/pool.d/www.conf

  # PHP 7.4
  apt-get install -y --allow-change-held-packages \
  php7.4 php7.4-bcmath php7.4-bz2 php7.4-cgi php7.4-cli php7.4-common php7.4-curl php7.4-dba php7.4-dev \
  php7.4-enchant php7.4-fpm php7.4-gd php7.4-gmp php7.4-imap php7.4-interbase php7.4-intl php7.4-json php7.4-ldap \
  php7.4-mbstring php7.4-mysql php7.4-odbc php7.4-opcache php7.4-pgsql php7.4-phpdbg php7.4-pspell php7.4-readline \
  php7.4-snmp php7.4-soap php7.4-sqlite3 php7.4-sybase php7.4-tidy php7.4-xdebug php7.4-xml php7.4-xmlrpc php7.4-xsl \
  php7.4-zip php7.4-imagick php7.4-memcached php7.4-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.4/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.4/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.4/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.4/cli/php.ini

  # Configure Xdebug
  echo "xdebug.mode = debug" >> /etc/php/7.4/mods-available/xdebug.ini
  echo "xdebug.discover_client_host = true" >> /etc/php/7.4/mods-available/xdebug.ini
  echo "xdebug.client_port = 9003" >> /etc/php/7.4/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/7.4/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/7.4/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/7.4/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/7.4/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/7.4/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/7.4/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/7.4/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/7.4/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/7.4/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.4/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/7.4/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/7.4/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.4/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/7.4/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/7.4/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/7.4/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/7.4/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/7.4/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/7.4/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/7.4/fpm/pool.d/www.conf

  # PHP 8.0
  apt-get install -y --allow-change-held-packages \
  php8.0 php8.0-bcmath php8.0-bz2 php8.0-cgi php8.0-cli php8.0-common php8.0-curl php8.0-dba php8.0-dev \
  php8.0-enchant php8.0-fpm php8.0-gd php8.0-gmp php8.0-imap php8.0-interbase php8.0-intl php8.0-ldap \
  php8.0-mbstring php8.0-mysql php8.0-odbc php8.0-opcache php8.0-pgsql php8.0-phpdbg php8.0-pspell php8.0-readline \
  php8.0-snmp php8.0-soap php8.0-sqlite3 php8.0-sybase php8.0-tidy php8.0-xdebug php8.0-xml php8.0-xmlrpc php8.0-xsl \
  php8.0-zip php8.0-memcached php8.0-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.0/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.0/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.0/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.0/cli/php.ini

  # Configure Xdebug
  echo "xdebug.mode = debug" >> /etc/php/8.0/mods-available/xdebug.ini
  echo "xdebug.discover_client_host = true" >> /etc/php/8.0/mods-available/xdebug.ini
  echo "xdebug.client_port = 9003" >> /etc/php/8.0/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/8.0/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/8.0/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.0/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.0/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/8.0/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/8.0/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/8.0/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.0/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/8.0/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.0/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/8.0/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.0/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/8.0/fpm/pool.d/www.conf

  # PHP 8.1
  apt-get install -y --allow-change-held-packages \
  php8.1 php8.1-bcmath php8.1-bz2 php8.1-cgi php8.1-cli php8.1-common php8.1-curl php8.1-dba php8.1-dev \
  php8.1-enchant php8.1-fpm php8.1-gd php8.1-gmp php8.1-imap php8.1-interbase php8.1-intl php8.1-ldap \
  php8.1-mbstring php8.1-mysql php8.1-odbc php8.1-opcache php8.1-pgsql php8.1-phpdbg php8.1-pspell php8.1-readline \
  php8.1-snmp php8.1-soap php8.1-sqlite3 php8.1-sybase php8.1-tidy php8.1-xdebug php8.1-xml php8.1-xmlrpc php8.1-xsl \
  php8.1-zip php8.1-memcached php8.1-redis

  # Configure php.ini for CLI
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.1/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.1/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.1/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.1/cli/php.ini

  # Configure Xdebug
  echo "xdebug.mode = debug" >> /etc/php/8.1/mods-available/xdebug.ini
  echo "xdebug.discover_client_host = true" >> /etc/php/8.1/mods-available/xdebug.ini
  echo "xdebug.client_port = 9003" >> /etc/php/8.1/mods-available/xdebug.ini
  echo "xdebug.max_nesting_level = 512" >> /etc/php/8.1/mods-available/xdebug.ini
  echo "opcache.revalidate_freq = 0" >> /etc/php/8.1/mods-available/opcache.ini

  # Configure php.ini for FPM
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.1/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.1/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/8.1/fpm/php.ini
  sed -i "s/;max_input_vars = .*/max_input_vars = 1500/" /etc/php/8.1/fpm/php.ini
  sed -i "s/max_execution_time = .*/max_execution_time = 240/" /etc/php/8.1/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.1/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/8.1/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/8.1/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.1/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/8.1/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.1/fpm/php.ini
  printf "[curl]\n" | tee -a /etc/php/8.1/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.1/fpm/php.ini

  # Configure FPM
  sed -i "s/user = www-data/user = ${USER}/" /etc/php/8.1/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/8.1/fpm/pool.d/www.conf
  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/8.1/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/8.1/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/8.1/fpm/pool.d/www.conf

  # Disable old PHP FPM
  #systemctl disable php7.0-fpm
  #systemctl disable php7.1-fpm
  #systemctl disable php7.2-fpm
  #systemctl disable php7.3-fpm
  #systemctl disable php7.4-fpm

  # PHP 8.0
  apt-get install -y --allow-change-held-packages \
  php8.0 php8.0-bcmath php8.0-bz2 php8.0-cgi php8.0-cli php8.0-common php8.0-curl php8.0-dba php8.0-dev \
  php8.0-enchant php8.0-fpm php8.0-gd php8.0-gmp php8.0-imap php8.0-interbase php8.0-intl php8.0-ldap \
  php8.0-mbstring php8.0-mysql php8.0-odbc php8.0-opcache php8.0-pgsql php8.0-phpdbg php8.0-pspell php8.0-readline \
  php8.0-snmp php8.0-soap php8.0-sqlite3 php8.0-sybase php8.0-tidy php8.0-xdebug php8.0-xml php8.0-xsl php8.0-zip \
  php8.0-memcached

  update-alternatives --set php /usr/bin/php8.0
  update-alternatives --set php-config /usr/bin/php-config8.0
  update-alternatives --set phpize /usr/bin/phpize8.0

  # Install Composer
  curl -sS https://getcomposer.org/installer | php
  mv composer.phar /usr/local/bin/composer
  chown -R ${USER}:${USER} /home/${USER}/.config

  # Install Global Packages
  sudo su ${USER} <<'EOF'
  /usr/local/bin/composer global require "laravel/envoy=^2.0"
  /usr/local/bin/composer global require "laravel/installer=^4.0.2"
  /usr/local/bin/composer global require "laravel/spark-installer=dev-master"
  /usr/local/bin/composer global require "slince/composer-registry-manager=^2.0"
  /usr/local/bin/composer global require tightenco/takeout
EOF

  # Set Some PHP CLI Settings
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.0/cli/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.0/cli/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.0/cli/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.0/cli/php.ini

  # Install Apache
  #apt-get install -y apache2 libapache2-mod-fcgid
  #sed -i "s/www-data/${USER}/" /etc/apache2/envvars

  # Enable FPM
  #a2enconf php8.0-fpm

  # Assume user wants mode_rewrite support
  #sudo a2enmod rewrite

  # Turn on HTTPS support
  #sudo a2enmod ssl

  # Turn on proxy & fcgi
  #sudo a2enmod proxy proxy_fcgi

  # Turn on headers support
  #sudo a2enmod headers actions alias

  # Add Mutex to config to prevent auto restart issues
  #if [ -z "$(grep '^Mutex posixsem$' /etc/apache2/apache2.conf)" ]
  #then
  #    echo 'Mutex posixsem' | sudo tee -a /etc/apache2/apache2.conf
  #fi

  #a2dissite 000-default
  #systemctl disable apache2

  # Install Nginx
  apt-get install -y --allow-downgrades --allow-remove-essential --allow-change-held-packages nginx

  rm /etc/nginx/sites-enabled/default
  rm /etc/nginx/sites-available/default

  # Create a configuration file for Nginx overrides.
  mkdir -p /home/${USER}/.config/nginx
  chown -R ${USER}:${USER} /home/${USER}
  touch /home/${USER}/.config/nginx/nginx.conf
  ln -sf /home/${USER}/.config/nginx/nginx.conf /etc/nginx/conf.d/nginx.conf

  # Setup Some PHP-FPM Options
  sed -i "s/error_reporting = .*/error_reporting = E_ALL/" /etc/php/8.0/fpm/php.ini
  sed -i "s/display_errors = .*/display_errors = On/" /etc/php/8.0/fpm/php.ini
  sed -i "s/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/" /etc/php/8.0/fpm/php.ini
  sed -i "s/memory_limit = .*/memory_limit = 512M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/upload_max_filesize = .*/upload_max_filesize = 100M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/8.0/fpm/php.ini
  sed -i "s/;date.timezone.*/date.timezone = UTC/" /etc/php/8.0/fpm/php.ini

  printf "[openssl]\n" | tee -a /etc/php/8.0/fpm/php.ini
  printf "openssl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.0/fpm/php.ini

  printf "[curl]\n" | tee -a /etc/php/8.0/fpm/php.ini
  printf "curl.cainfo = /etc/ssl/certs/ca-certificates.crt\n" | tee -a /etc/php/8.0/fpm/php.ini

  # Disable XDebug On The CLI
  sudo phpdismod -s cli xdebug

  # Set The Nginx & PHP-FPM User
  sed -i "s/user www-data;/user ${USER};/" /etc/nginx/nginx.conf
  sed -i "s/# server_names_hash_bucket_size.*/server_names_hash_bucket_size 64;/" /etc/nginx/nginx.conf

  sed -i "s/user = www-data/user = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/group = www-data/group = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf

  sed -i "s/listen\.owner.*/listen.owner = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/listen\.group.*/listen.group = ${USER}/" /etc/php/8.0/fpm/pool.d/www.conf
  sed -i "s/;listen\.mode.*/listen.mode = 0666/" /etc/php/8.0/fpm/pool.d/www.conf

  service nginx restart
  service php7.0-fpm restart
  service php7.1-fpm restart
  service php7.2-fpm restart
  service php7.3-fpm restart
  service php7.4-fpm restart
  service php8.0-fpm restart

  # Add User To WWW-Data
  usermod -a -G www-data ${USER}
  id ${USER}
  groups ${USER}

  # Install wp-cli
  curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
  chmod +x wp-cli.phar
  mv wp-cli.phar /usr/local/bin/wp

  # Install Drush Launcher.
  curl --silent --location https://github.com/drush-ops/drush-launcher/releases/download/0.6.0/drush.phar --output drush.phar
  chmod +x drush.phar
  mv drush.phar /usr/local/bin/drush
  drush self-update

  # Install Drupal Console Launcher.
  #curl --silent --location https://drupalconsole.com/installer --output drupal.phar
  #chmod +x drupal.phar
  #mv drupal.phar /usr/local/bin/drupal

  # Add Composer Global Bin To Path
  printf "\nPATH=\"$(sudo su - ${USER} -c 'composer config -g home 2>/dev/null')/vendor/bin:\$PATH\"\n" | tee -a /home/${USER}/.profile
fi

# Install Node

apt-get install -y nodejs
/usr/bin/npm install -g npm
/usr/bin/npm install -g yarn

# Install SQLite
apt-get install -y sqlite3 libsqlite3-dev

if "$SKIP_MYSQL"; then
  echo "SKIP_MYSQL is being used, so we're not installing MySQL"
  apt-get install -y mysql-client
else
  # Install MySQL
  echo "mysql-server mysql-server/root_password password secret" | debconf-set-selections
  echo "mysql-server mysql-server/root_password_again password secret" | debconf-set-selections
  apt-get install -y mysql-server

  # Configure MySQL 8 Remote Access and Native Pluggable Authentication
  cat > /etc/mysql/conf.d/mysqld.cnf << EOF
[mysqld]
bind-address = 0.0.0.0
default_authentication_plugin = mysql_native_password
EOF

  # Configure MySQL Password Lifetime
  echo "default_password_lifetime = 0" >> /etc/mysql/mysql.conf.d/mysqld.cnf

  # Configure MySQL Remote Access
  sed -i '/^bind-address/s/bind-address.*=.*/bind-address = 0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf
  service mysql restart

  export MYSQL_PWD=secret

  mysql --user="root" -e "ALTER USER 'root'@'localhost' IDENTIFIED BY 'secret';"
  mysql --user="root" -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;"
  mysql --user="root" -e "CREATE USER 'simon'@'0.0.0.0' IDENTIFIED BY 'secret';"
  mysql --user="root" -e "CREATE USER 'simon'@'%' IDENTIFIED BY 'secret';"
  mysql --user="root" -e "GRANT ALL PRIVILEGES ON *.* TO 'simon'@'0.0.0.0' WITH GRANT OPTION;"
  mysql --user="root" -e "GRANT ALL PRIVILEGES ON *.* TO 'simon'@'%' WITH GRANT OPTION;"
  mysql --user="root" -e "FLUSH PRIVILEGES;"
  # mysql --user="root" -e "CREATE DATABASE homestead character set UTF8mb4 collate utf8mb4_bin;"

  sudo tee /home/${USER}/.my.cnf <<EOL
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_bin
EOL
  service mysql restart
fi

# Install Redis, Memcached, & Beanstalk
apt-get install -y redis-server memcached beanstalkd
systemctl enable redis-server
service redis-server start

# Configure Beanstalkd
sed -i "s/#START=yes/START=yes/" /etc/default/beanstalkd

# Install & Configure MailHog
if [[ "$ARCH" == "aarch64" ]]; then
  wget --quiet -O /usr/local/bin/mailhog https://github.com/mailhog/MailHog/releases/download/v1.0.1/MailHog_linux_arm
else
  wget --quiet -O /usr/local/bin/mailhog https://github.com/mailhog/MailHog/releases/download/v1.0.1/MailHog_linux_amd64
fi
chmod +x /usr/local/bin/mailhog

sudo tee /etc/systemd/system/mailhog.service <<EOL
[Unit]
Description=Mailhog
After=network.target
[Service]
User=${USER}
ExecStart=/usr/bin/env /usr/local/bin/mailhog > /dev/null 2>&1 &
[Install]
WantedBy=multi-user.target
EOL

systemctl daemon-reload
systemctl enable mailhog

# Install & Configure Postfix
echo "postfix postfix/mailname string homestead.test" | debconf-set-selections
echo "postfix postfix/main_mailer_type string 'Internet Site'" | debconf-set-selections
apt-get install -y postfix
sed -i "s/relayhost =/relayhost = [localhost]:1025/g" /etc/postfix/main.cf
/etc/init.d/postfix reload

# One last upgrade check
apt-get upgrade -y

# Clean Up
apt -y autoremove
apt -y clean
chown -R ${USER}:${USER} /home/${USER}
chown -R ${USER}:${USER} /usr/local/bin

# delete docs packages
dpkg --list \
    | awk '{ print $2 }' \
    | grep -- '-doc$' \
    | xargs apt-get -y purge;

apt-get -y autoremove;
apt-get -y clean;

# What are you doing Ubuntu?
# https://askubuntu.com/questions/1250974/user-root-cant-write-to-file-in-tmp-owned-by-someone-else-in-20-04-but-can-in
sysctl fs.protected_regular=0

# Enable Swap Memory (https://askubuntu.com/a/1075516)
swapoff /swapfile
rm /swapfile
fallocate -l 16G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

## My own stuff

```bash
# Remove sudoku game
sudo apt remove --auto-remove --purge -y gnome-sudoku

# Disable bluetooth startup
sudo sed -i "s/AutoEnable=true/AutoEnable=false/" /etc/bluetooth/main.conf
sudo systemctl disable bluetooth.service

# Add section for homestead domains
sudo tee -a /etc/hosts <<EOL

#### HOMESTEAD-SITES-BEGIN
#### HOMESTEAD-SITES-END
EOL

# Let TYPO3 process PDF and AI files
# see https://stackoverflow.com/a/60145381
sed -i 's/<policy domain="coder" rights="none" pattern="PDF" \/>/<!--<policy domain="coder" rights="none" pattern="PDF" \/>-->/' /etc/ImageMagick-6/policy.xml
```

### Make Ubuntu fully dark, not just half...

https://www.omgubuntu.co.uk/2020/04/enable-full-dark-mode-in-ubuntu-20-04

```bash
sudo apt install -y gnome-shell-extensions gnome-shell-extension-prefs gnome-tweaks
```

### Adjust ubuntu internal keyboad shortcuts

* Disbale "Auf Arbeitsfläche darunter verschieben"
* Disbale "Auf Arbeitsfläche darüber verschieben"
