# Amazon Web Service Notes

I created this document for myself to setup EC2 instances. No warranty.

## Initiate and update the environment.

```
sudo yum update -y
sudo yum install -y httpd24 php56 mysql55-server php56-mysqlnd php56-gd php-pear
```

## Ensure both httpd and mysqld start on reboots.

```
sudo chkconfig httpd on
sudo chkconfig mysqld on
```

# Download and setup WordPress.

```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
```

# Add group www and add ec2-use and apache to www group.

```
sudo groupadd www
sudo usermod -a -G www ec2-user
sudo usermod -a -G www apache
```

# Setup no-sites

```
sudo chown -R apache /var/www
sudo chgrp -R www /var/www
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} +
find /var/www -type f -exec sudo chmod 0664 {} +
```

# Setup sites (virtual hosts) and initial WordPress files.

```
sudo mkdir -p /var/www-sites

sudo chown -R apache /var/www-sites
sudo chgrp -R www /var/www-sites
sudo chmod 2775 /var/www-sites


mkdir -p /var/www-sites/dubelyoo.com/html
mkdir -p /var/www-sites/fisholo.com/html
mkdir -p /var/www-sites/yakimaflyfishers.org/html/wp
mkdir -p /var/www-sites/tkmtwo.com/html

cp -r wordpress/* /var/www-sites/dubelyoo.com/html
cp -r wordpress/* /var/www-sites/yakimaflyfishers.org/html/wp

find /var/www-sites -type d -exec sudo chmod 2775 {} +
find /var/www-sites -type f -exec sudo chmod 0664 {} +
```

# Initialize MySQL

```
sudo service mysqld start
sudo mysql_secure_installation

mysql -u root -p

CREATE USER 'dubelyoo'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'yakimaflyfishers'@'localhost' IDENTIFIED BY 'password';

CREATE DATABASE `dubelyoo-wordpress-db`;
CREATE DATABASE `yakimaflyfishers-wordpress-db`;


GRANT SELECT, INSERT, UPDATE, DELETE ON `dubelyoo-wordpress-db`.* TO "dubelyoo"@"localhost";
GRANT SELECT, INSERT, UPDATE, DELETE ON `yakimaflyfishers-wordpress-db`.* TO "yakimaflyfishers"@"localhost";

FLUSH PRIVILEGES;

exit

sudo service mysqld stop
```

# Configure WordPress for each site.
```
https://api.wordpress.org/secret-key/1.1/salt/

cp /var/www-sites/dubelyoo.com/html/wp-config-sample.php /var/www-sites/dubelyoo.com/html/wp-config.php
vim /var/www-sites/dubelyoo.com/html/wp-config.php

https://api.wordpress.org/secret-key/1.1/salt/

cp /var/www-sites/yakimaflyfishers.com/html/wp-config-sample.php /var/www-sites/yakimaflyfishers.com/html/wp-config.php
vim /var/www-sites/yakimaflyfishers.com/html/wp-config.php
```

# Edit httpd.conf for sites

```
sudo vim /etc/httpd/conf/httpd.conf



sudo vim /etc/hosts
172.30.0.50 ip-172-30-0-50

sudo vim /etc/httpd/conf/httpd.conf
ServerName ip-172-30-0-50:80
```



# Start httpd and mysqld

```
sudo service httpd start
sudo service mysqld start

sudo yum-config-manager --enable epel
sudo yum install -y phpMyAdmin

sudo sed -i -e 's/127.0.0.1/64.33.177.215/g' /etc/httpd/conf.d/phpMyAdmin.conf

curl -sS https://getcomposer.org/installer | php -- --install-dir=bin --filename=composer
sudo yum install -y git
```
