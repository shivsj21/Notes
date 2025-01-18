# How To Secure Apache with Let's Encrypt on Ubuntu
- First add public ip in A record go.godaddy.com
- Second setup Laravel in ubuntu as per gitrepo 
    https://github.com/shivarna1/Notes/blob/main/Install-Laravel-Apache-Ubuntu.md

### Add domain name and serverAlias 
```
vim /etc/apache2/sites-available/laravel-app.conf
```
```
<VirtualHost *:80>
    ServerName  test.siddhvastu.com 				    //add domain name
    DocumentRoot /var/www/html/laravel-app/public
    ServerAlias 159.203.175.132 				        // add public Ip
    <Directory /var/www/html/laravel-app/public>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
RewriteEngine on
RewriteCond %{SERVER_NAME} =test.siddhvastu.com [OR]
RewriteCond %{SERVER_NAME} =159.203.175.132
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```
### Restart Apache Server
```
sudo systemctl restart apache2
```
### Enable a specific site configuration
```
a2ensite test.siddhvastu.com.conf    		
```
### Check the syntax of your Apache configuration files 
```
apache2ctl configtest
```						
### How To Secure Apache with Let's Encrypt on Ubuntu
https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu

#### Installing Certbot
```
sudo apt update
```
```
sudo apt install certbot python3-certbot-apache
```
#### Obtaining an SSL Certificate
```
sudo certbot --apache
```
#### Verifying Certbot Auto-Renewal
```
sudo systemctl status certbot.timer
```
