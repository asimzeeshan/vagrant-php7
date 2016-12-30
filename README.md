# vagrant-php7
PHP7 box for Vagrant

Creates a Vagrant box using ubuntu/xenial64 (Ubuntu 16.04 LTS) as base and sets up the following

The following items are currently installed

- Apache 2.4.18
- PHP 7.0
- composer 1.3.0
- MySQL 5.7 & Sqlite 3.11.0)
- phpMyAdmin (don't boo please)

**Virtualization Related Notes:**
- vCPUs: 2
- vCPUs execution cap: 50%
- Memory: 1024MB
- virtualbox title: php7dev (xenial64)

**Document root:** /var/www/html
**VM Hostname:** php7.local
**Automatic Host Entries:**
- devbox.local
- admin.php7.local (not configured yet)
- php7.local
- mailcatcher.php7.local
- phpmyadmin.php7.local
