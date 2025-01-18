first add public ip in A record go.godaddy.com
second setup Laravel in ubuntu as per gitrepo

root@Laravel:~# vim /etc/apache2/sites-available/laravel-app.conf
root@Laravel:~# cat /etc/apache2/sites-available/laravel-app.conf
<VirtualHost *:80>
    ServerName  test.siddhvastu.com 				//add domain name
    DocumentRoot /var/www/html/laravel-app/public
    ServerAlias 159.203.175.132 				// add public Ip
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

root@Laravel:~# sudo systemctl restart apache2

root@Laravel:~# a2ensite test.siddhvastu.com.conf    		//enable a specific site configuration
root@Laravel:~# apache2ctl configtest						//check the syntax of your Apache configuration files to ensure there are no errors before restarting Apache

https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu
root@Laravel:~# sudo apt update
root@Laravel:~# sudo apt install certbot python3-certbot-apache
root@Laravel:~# sudo certbot --apache
root@Laravel:~# sudo systemctl status certbot.timer
