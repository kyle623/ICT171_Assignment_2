# Building an Amazon EC2 WordPress Web Server With DNS and SSL/TLS

  image 
- Instance type should be default 
- Name your server
- You can leave most settings default. Ensure the instance is in the correct region and subnet
- Configure Security Group to allow HTTP, HTTPS and SSH in the network settings
- Key Pair Select an existing key pair or create a new one. Make sure you download the private key (.pem) you will need this to SSH into the instance
Finally, Click launch and wait for your instance to boot 
 (Using MobaXterm on Windows)
- Open MobaXterm and click on session > newsession. Choose SSH as the session type.
- In session settings, fill in the remote host with your instance’s Elastic IP, and Specify username as ubuntu
- Un  ver. Check if apaceh is running by checkkign the satus or visiting the server’s IP in a browser 
visit http://elasticiphere  you should see the default Apache It works! page.

## 6. install PHP  
 sudo apt install php libapache2-mod-php php-mysql  
 sudo apt install mysql-server
```

## 7. Configure MYSQL Server
We need to change the MySQL login to password from auth
```
 sudo mysql - l database that WP will use. call it wp (lowercase)
```e
```
 rivilges on the database 'wp' to the newly created user
```
GRANT ALL PRIVILEGES ON wp.* TO 'wp_user'@localhost;
```
and exit MySQL
f

## 12. Unzip 
Now extract the folder
```
tar -xvf latest.tqw 
 ss files into the web server’s document root.
```
sudo mv wordpress/ /var/www/html

```

## 14. run the WP Web installer 
We can run WordPress’s web-based setup. In a web browser, navigate to http://elasticip/wordpress
follow the form
- Database Name: wp
- Password:  
- Database Host: localhost
- Table Prefix: wp_ lly
```wordpress
```ew l th   activate 
- In WP admin, go to Code Snippets and click add Snippet.
- Paste your script directly from the GitHub
- Pick HTML Snippet
- Toggle Active and hit Save Snippet
- Configure to Short Code and copy wpcode id to clipboard and add to site using a paragraph object
