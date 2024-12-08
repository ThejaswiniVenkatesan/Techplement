# Techplement
Techplement Week 1 Tasks
Task Overview
The objective is to deploy a WordPress application using monolithic and microservices architectures on AWS.

Monolithic Architecture
Single EC2 instance.
Both WordPress and MySQL deployed on the same instance.
Microservices Architecture
Two EC2 instances:
One for WordPress.
One for MySQL.
Separate roles for application and database layers.
Prerequisites
AWS Free Tier Account
Ensure your AWS account has free-tier eligible resources.
Instance Configuration
Instance type: t2.micro
AMI: Ubuntu 22.04 or similar.
Architecture Details
Monolithic Architecture
Single instance deployment.
WordPress and MySQL run on the same server.
Microservices Architecture
WordPress and MySQL are deployed on separate EC2 instances.
MySQL accepts remote connections from the WordPress instance.
Steps
1. Launch EC2 Instances
Use the AWS Management Console to launch instances.
Configure security groups:
Allow SSH (Port 22), HTTP (Port 80), and HTTPS (Port 443) for WordPress.
Allow MySQL (Port 3306) for the MySQL instance.
2. Monolithic Setup
Install Required Packages

bash
Copy code
sudo apt update
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql -y
sudo systemctl enable apache2 mysql
Set Up MySQL Database

sql
Copy code
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'SecureP@ssword123';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
Deploy WordPress

Download WordPress:
bash
Copy code
cd /tmp
wget https://wordpress.org/latest.tar.gz
tar -xvzf latest.tar.gz
sudo mv wordpress/* /var/www/html/
Update permissions:
bash
Copy code
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/
Configure wp-config.php with:
php
Copy code
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'SecureP@ssword123' );
define( 'DB_HOST', 'localhost' );
Access WordPress

Navigate to http://<EC2_Public_IP> in your browser and complete the setup wizard.
3. Microservices Setup
MySQL Instance

Install and configure MySQL as in monolithic setup.
Modify /etc/mysql/mysql.conf.d/mysqld.cnf:
css
Copy code
bind-address = 0.0.0.0
Restart MySQL:
bash
Copy code
sudo systemctl restart mysql
Create a database user:
sql
Copy code
CREATE USER 'wordpressuser'@'%' IDENTIFIED BY 'SecureP@ssword123';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'%';
FLUSH PRIVILEGES;
WordPress Instance

Install Apache and PHP:
bash
Copy code
sudo apt update
sudo apt install apache2 php libapache2-mod-php php-mysql wget -y
Download and configure WordPress as in the monolithic setup, but update DB_HOST in wp-config.php:
php
Copy code
define( 'DB_HOST', '<MySQL_Instance_Private_IP>' );
Access WordPress

Navigate to http://<WordPress_Instance_Public_IP> in your browser and complete the setup wizard.
4. Verify
Test database connection using:
php
Copy code
<?php
$conn = new mysqli('<MySQL_Instance_Private_IP>', 'wordpressuser', 'SecureP@ssword123', 'wordpress');
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully";
?>
Post-Deployment
Record the process with screenshots or screen recordings.
Terminate AWS resources:
bash
Copy code
aws ec2 terminate-instances --instance-ids <instance-id>
Files in This Repository
week1-tasks/
Monolithic: Contains setup commands and configuration files.
Microservices: Contains database connection script and WordPress files.
Screenshots: Deployment steps and configurations.
