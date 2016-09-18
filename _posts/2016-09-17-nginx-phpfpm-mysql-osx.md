---
layout: post
title: "Install Nginx, PHP-FPM, Mysql on Mac OS X"
author: "Hans Phung"
categories: server macosx
---

# Xcode
Make sure you have the latest version of XCode installed. [Available from the AppStore](https://itunes.apple.com/gb/app/xcode/id497799835?mt=12).

Install the Xcode Command Line Tools:
```
xcode-select --install
```

# Homebrew
[Homebrew](http://brew.sh/) is the missing package manager for OSX.
Download and install using the following command:

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Check for any problems:

```
brew doctor
```

Update and Upgrade ```brew``` formulas:

```
brew update && brew upgarde
```

# Install nginx

```
brew install nginx
```

### setup auto start

```
sudo cp /usr/local/opt/nginx/*.plist /Library/LaunchDaemons/
sudo chown root:wheel /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

### start from command line

```
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

### stop from command line

```
sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Nginx will listen on port 8080 by default.

# Install PHP-FPM

```
brew tap homebrew/dupes
brew install --without-apache --with-fpm --with-mysql php70
```

## setup auto start

```
mkdir -p ~/Library/LaunchAgents
cp /usr/local/Cellar/php70/7.0.10_1/homebrew.mxcl.php70.plist ~/Library/LaunchAgents/
```

start PHP-FPM:

```
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php70.plist
```

verify that PHP-FPM is listening on port 9000:

```
lsof -Pni4 | grep LISTEN | grep php
```

# Setup MySQL

```
brew install mysql
```

## setup auto start

```
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents/
```

then start the mysql server:

```
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

Secure the installation

```
mysql_secure_installation
```

Test the connection

```
mysql -uroot -p
```

# Command aliases

Dowload the aliases:

```
curl -L https://gist.githubusercontent.com/hanhpv/104cf0ebb9ddd887858543f5b22ac55d/raw/c85c3a931b58de428e9211f61cd610cb166842f7/osx-nginx-phpfpm-mysql-aliases -o /tmp/.aliases
```

For bash:

```
cat /tmp/.aliases >> ~/.profile
```

Close and reopen the terminal or type ```source ~/.profile``` to reload the profile.

The following commands are now available:

### Nginx
```
nginx.start
nginx.stop
nginx.restart
```

### PHP-FPM
```
php-fpm.start
php-fpm.stop
php-fpm.restart
```

### mysql
```
mysql.start
mysql.stop
mysql.restart
```

### Nginx Logs
```
nginx.logs.error
nginx.logs.access
nginx.logs.default.access
nginx.logs.default-ssl.access
nginx.logs.phpmyadmin.access
```
