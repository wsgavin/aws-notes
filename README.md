# Amazon Web Service Notes

I created this document for myself to setup EC2 instances. No warranty.

http://www.nouveauframework.com/blog/ultimate-guide-to-hosting-wordpress-on-aws-ec2-for-complete-beginners/

## Initial Configuration

    tzselect
    
    sudo vim /etc/sysconfig/clock
    
    ZONE="America/Chicago"
    UTC=false
    
    sudo ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime

**NB** Later we will update the php.ini file with the timezone as well.
    

## Initiate and update the environment.

    sudo yum update -y
    sudo yum install -y gcc make git
    sudo yum install -y httpd24 php56
    sudo yum install -y php56-devel php56-mysqlnd php56-pdo php56-mbstring php56-gd
    sudo yum install -y php-pear
    sudo pear install -all Log
    sudo yum install -y pcre-devel
    sudo yum install -y php56-opcache
    sudo yum install -y re2c
    sudo yum install -y mod24_ssl
    sudo yum install -y memcached
    sudo yum install -y php56-pecl-memcached
    sudo yum install -y ImageMagick
    sudo yum install -y ImageMagick-devel
    sudo pecl install imagick
    sudo yum install -y mysql56-server
    sudo yum-config-manager --enable epel
    sudo yum install -y phpmyadmin
	
	# sudo yum install -y httpd24 php56 mysql56-server php56-mysqlnd php56-gd php-pear git
	
	
    WARNING: channel "pear.php.net" has updated its protocols, use "pear channel-update pear.php.net" to update
    WARNING: "pear/DB" is deprecated in favor of "pear/MDB2"
    WARNING: "pear/Auth_SASL" is deprecated in favor of "pear/Auth_SASL2"


## Ensure both httpd and mysqld start on reboots.

    sudo chkconfig httpd on
    sudo chkconfig mysqld on

# Download and setup WordPress.

    wget https://wordpress.org/latest.tar.gz
    tar -xzf latest.tar.gz

# Add group www and add ec2-use and apache to www group.

    sudo groupadd www
    sudo usermod -a -G www ec2-user
    sudo usermod -a -G www apache
    exit

# Setup no-sites

    sudo chown -R apache /var/www
    sudo chgrp -R www /var/www
    sudo chmod 2775 /var/www
    find /var/www -type d -exec sudo chmod 2775 {} +
    find /var/www -type f -exec sudo chmod 0664 {} +

# Add users

    sudo useradd warren
    sudo passwd warren
    sudo useradd fisholo
    sudo passwd fisholo
    sudo useradd tkmtwo
    sudo passwd tkmtwo
    
    sudo chmod 711 /home/tkmtwo/
    sudo mkdir /home/tkmtwo/public_html
    sudo chown tkmtwo:tkmtwo /home/tkmtwo/public_html
    sudo chmod 755 /home/tkmtwo/public_html



# Setup sites (virtual hosts) and initial WordPress files.

    sudo mkdir -p /var/www-sites
    sudo mkdir -p /var/www-sites/dubelyoo.com/html
    sudo mkdir -p /var/www-sites/fisholo.com/html
    
    sudo cp -r wordpress/* /var/www-sites/dubelyoo.com/html

    sudo chown -R apache /var/www-sites
    sudo chgrp -R www /var/www-sites
    sudo chmod 2775 /var/www-sites

    find /var/www-sites -type d -exec sudo chmod 2775 {} +
    find /var/www-sites -type f -exec sudo chmod 0664 {} +

# Initialize MySQL

    sudo service mysqld start
    sudo mysql_secure_installation

    mysql -u root -p

    CREATE USER 'dubelyoo'@'localhost' IDENTIFIED BY 'password';
    CREATE DATABASE `dubelyoo-wordpress`;
    GRANT ALL PRIVILEGES ON `dubelyoo-wordpress`.* TO "dubelyoo"@"localhost";
    FLUSH PRIVILEGES;
    exit

    sudo service mysqld stop

# Configure WordPress for each site.
https://api.wordpress.org/secret-key/1.1/salt/

    cp /var/www-sites/dubelyoo.com/html/wp-config-sample.php /var/www-sites/dubelyoo.com/html/wp-config.php
    vim /var/www-sites/dubelyoo.com/html/wp-config.php

# Edit httpd.conf for sites

    sudo vim /etc/httpd/conf.d/vhost.conf
    
    <VirtualHost *:80>
    
      ServerName dubelyoo.com
      DocumentRoot /var/www-sites/dubelyoo.com/html
      <Directory /var/www-sites/dubelyoo.com/html>
        Order allow,deny
        Allow from all
        Require all granted
        AllowOverride All
      </Directory>
    
    </VirtualHost>
    
    <VirtualHost *:80>
    
      ServerName fisholo.com
      DocumentRoot /var/www-sites/fisholo.com/html
      <Directory /var/www-sites/fisholo.com/html>
        Order allow,deny
        Allow from all
        Require all granted
        AllowOverride All
      </Directory>
    
    </VirtualHost>
    
    <VirtualHost *:80>
    
      ServerName tkmtwo.com
      DocumentRoot /home/tkmtwo/public_html
      <Directory /home/tkmtwo/public_html>
        Order allow,deny
        Allow from all
        Require all granted
        AllowOverride All
      </Directory>
    
    </VirtualHost>
    
    
    sudo vim /etc/httpd/conf.d/userdir.conf

    sudo vim /etc/hosts
    172.30.0.50 ip-172-30-0-50

    sudo vim /etc/httpd/conf/httpd.conf
    ServerName ip-172-30-0-50:80
    
    sudo vim /etc/php.ini
    
    date.timezone = "America/Chicago" 
    extension=imagick.so
    
    sudo sed -i -e 's/127.0.0.1/64.33.177.215/g' /etc/httpd/conf.d/phpMyAdmin.conf


# Start httpd and mysqld

    sudo service httpd start
    sudo service mysqld start
    
# Setup Wordpress
