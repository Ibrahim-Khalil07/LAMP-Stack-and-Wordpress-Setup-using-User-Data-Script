# LAMP Stack Setup with WordPress on Amazon Linux 2

This script automates the process of setting up a LAMP (Linux, Apache, MySQL, PHP) stack and WordPress on an Amazon Linux 2 EC2 instance. It assumes you have already launched an EC2 instance and have SSH access to it.

## Script Explanation

### Step 1: System Update
The script begins by updating the system packages using sudo yum update -y.

    sudo yum update -y

### Step 2: Install Software Packages

MariaDB (MySQL) Server: MariaDB is a popular open-source relational database management system. 
This script installs the MariaDB server.
           
    sudo yum install mariadb-server.x86_64 -y

Apache HTTP Server (httpd): Apache HTTP server is a widely used web server software.

    sudo yum install httpd -y

PHP 7.4: PHP is a server-side scripting language used for web development. This script installs PHP 7.4 along with necessary extensions.

    sudo amazon-linux-extras install php7.4

### Step 3: Start and Enable Services

The script starts and enables the following services:

MariaDB: MariaDB service is started and set to start automatically on system boot.

    sudo systemctl enable mariadb 
    sudo systemctl start mariadb 

Apache HTTP Server (httpd): The Apache web server service is started and set to start automatically on system boot.

    sudo systemctl start httpd
    sudo systemctl enable httpd

PHP-FPM: PHP-FPM (PHP FastCGI Process Manager) is started and enabled to handle PHP requests.

    sudo systemctl enable php-fpm 
    sudo systemctl start php-fpm 

### Step 4: Create a Directory for WordPress

A directory /var/www/ is created, where WordPress files will be placed. The ownership of this directory is set to the ec2-user.

    sudo mkdir /var/www/
    sudo chown ec2-user:ec2-user /var/www/

### Step 5: Download and Configure WordPress

Download WordPress: WordPress is downloaded from the official website, extracted, and placed in /var/www/wordpress/.

    cd /var/www/
    wget http://wordpress.org/latest.tar.gz
    tar -xvf latest.tar.gz
    rm latest.tar.gz
    cd /var/
    
    sudo chown -R apache:apache /var/www/

    sudo mysql_install_db

### Step 6: Apache Configuration

Apache configuration for the server block (Virtual Host)
    
    cd /tmp/
    sudo echo "<VirtualHost *:80>
    ServerAdmin webmaster@yourdomain.com
    DocumentRoot /var/www/wordpress
    ServerName wordpress
    ServerAlias wordpress

    ErrorLog /var/log/httpd/wordpress-error.log
    CustomLog /var/log/httpd/wordpress-access.log combined

    <Directory /var/www/wordpress>
        AllowOverride All
        Require all granted
    </Directory>
    </VirtualHost>" > /etc/httpd/conf.d/wordpress.conf

Setting permissions 

    sudo chown root:root wordpress.conf
    sudo chmod 644 wordpress.conf
    sudo mv wordpress.conf /etc/httpd/conf.d/



### Step 7: Restart Services

All services are restarted to apply the configuration changes.

    sudo service httpd restart
    sudo service php-fpm restart
    sudo service mariadb restart

### Security Considerations

#### Hardcoded Credentials:
 This script uses hardcoded credentials for the MariaDB root user and WordPress database user. It is essential to change these credentials for security purposes.

#### Production Use:
 This script is intended for demonstration or development environments. For production use, consider additional security measures and best practices.

### Disclaimer
This script provides a basic setup for a LAMP stack with WordPress. Ensure you adapt it to your specific requirements and follow best practices for security and performance when deploying in a production environment.