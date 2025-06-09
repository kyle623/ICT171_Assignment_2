# Building an Amazon EC2 WordPress Web Server With DNS and SSL/TLS

## Kyle Cleofe 34211995

## 1. Intro
This guide explains how to setup a WordPress web server on Amazon EC2 from scratch. We will launch an Ubuntu EC2 Instance (t2.micro or any free virtual computer), install apache and mySQL and configure WordPress on it. We will associate a Domain Name and secure it with TLS using Let's Encrypt. By the end, you will have a Webserver on wordpress site running in your custom domain with HTTPS enabled. Use the Youtube link alternatively to follow through the steps

## 2. ⚙️ Launching an EC2 instance using Ubuntu 
First log in to the AWS management console and go to EC2 Service. Click "Launch Instance" to create a new virtual machine:
- Select Ubuntu server LTS image 
- Instance type should be default 
- Name your server
- You can leave most settings default. Ensure the instance is in the correct region and subnet
- Configure Security Group to allow HTTP, HTTPS and SSH in the network settings
- Key Pair Select an existing key pair or create a new one. Make sure you download the private key (.pem) you will need this to SSH into the instance
Finally, Click launch and wait for your instance to boot 

## 3. Assigning an Elastic IP
To make sure our ip is static:
- In the left navigation, click Elastic IPs
- Click Allocate Elastic IP address and confirm. A new Elastic IP will appear in your list.
- Select new Elastic IP, Actions> Associate Elastic IP. Choose your Instance EC2  from the dropdown, and click Associate

## 4. Connect via SSH (Using MobaXterm on Windows)
- Open MobaXterm and click on session > newsession. Choose SSH as the session type.
- In session settings, fill in the remote host with your instance’s Elastic IP, and Specify username as ubuntu
- Under Advanced SSH Settings, check use private key and select your downloaded pem key file. then click ok

## 5. Install apache2 
type the code below to install apache
```
 sudo apt update
 sudo apt install apache2
```
This will install the Apache2 web server. Check if apaceh is running by checkkign the satus or visiting the server’s IP in a browser 
visit http://elasticiphere  you should see the default Apache It works! page.

## 6. install PHP and Required Extensions
Wordpress runs on PHP  wso we need PHP and its MySQL extension on the server. We will also install MySQL server required for WP
```
 sudo apt install php libapache2-mod-php php-mysql  
 sudo apt install mysql-server
```

## 7. Configure MYSQL Server
We need to change the MySQL login to password from auth
```
 sudo mysql -u root
```
Once logged in paste this code in but CHANGE THE PASSWORD. Use a strong password.
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'yourpassword';
```

## 8. Create a new database user for WP
While in MySQL, Run to create new database user:
```
CREATE USER 'wp_user'@localhost IDENTIFIED BY 'yourpassword';
```

## 9. Create a database for WP
Now create a new sql database that WP will use. call it wp (lowercase)
```
CREATE DATABASE wp;
```

## 10. Grant all privilges on the database 'wp' to the newly created user
```
GRANT ALL PRIVILEGES ON wp.* TO 'wp_user'@localhost;
```
and exit MySQL

## 11. Download WordPress
Change to /tmp and use wget to get WordPress:
```
cd /tmp
wget https://wordpress.org/latest.tar.gz
```

## 12. Unzip 
Now extract the folder
```
tar -xvf latest.tar.gz
```

## 13. Move WP folder into Apache’s Document root
While in tmp, we need to place the WordPress files into the web server’s document root.
```
sudo mv wordpress/ /var/www/html

```

## 14. run the WP Web installer
We can run WordPress’s web-based setup. In a web browser, navigate to http://elasticip/wordpress
follow the form
- Database Name: wp
- Password: 
- Database Host: localhost
- Table Prefix: wp_
Then submit.
It will display a block of PHP code for you to manually add for the next step

## 15. Create the wp-config.php file manually
```
sudo nano /var/www/html/wordpress/wp-config.php
```
In the editor, paste the code that WordPress gave you. This code contains the database name, username, and password you entered, and it also includes unique secret keys for your installation. Save this file after pasting.

## 16. Complete the Wordpress installation
Go back to the WP web installer in your browser and click “Run the installation” . You will be asked to set up your new site:
- Site Title: enter a title
- Administrator Username: choose an admin user name 
- Password: set a strong password 
- Your Email
- Search Engine Visibility: do not allow indexing
Submit the form to install. You should see a Log In button verify you can log into the WP dash with the admin detauls you just created.

## 17. Configure Apache to Serve WordPress as the Root Site
Wordpress is a subpath right now. We want the site to be accessed at the root of our domain (or IP) without the /wordpress path by telling Apache to use the subdirectory as the document root.
```
sudo nano /etc/apache2/sites-available/000-default.conf
```
change the "DocumentRoot /var/www/html" to "DocumentRoot /var/www/html/wordpress"
Save and exit then restart apache
```
 sudo systemctl restart apache2
```

## 18. Link Your Domain Name via DNS and update apache config
Go to namecheap or DNS hosting servidce and create a new A record for your domain: point it to the Elastic IP address of your EC2 instance. For example:


Next, configure Apache to recognize and answer to your domain name. Edit the same file 000-default.conf again
Below the DocumentROot line add: 
```
 ServerName yourdomain.com
 ServerAlias www.yourdomain.com
```
save and exit

## 19. Restart Apache2
```
 sudo systemctl restart apache2
```

## 20. Update WP Address (URL) and Site address to the DNS name 
- go to http://yourdomain.com/wp-admin/ and login
- In WP sidebar Settings > general
- WordPress Address (URL) and Site Address (URL): Change both of these to full domain URL
- WordPress Address (URL): http://domain.com
- Site Address (URL): http://domain.com
- Change Timezone to UTC +8 then save changes

## 21. Install  SSL/TLS with lets encrypt
First, install Certbot and its Apache plugin on the server:
```
sudo apt-get update
sudo apt install certbot python3-certbot-apache
```

## 22. Obtain and Install an SSL cert with Certbot
```
sudo certbot --apache
```

## 23. Activate https on both names and final URL adjustments
