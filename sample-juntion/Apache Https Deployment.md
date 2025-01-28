# SOP for Deploying an Application on Apache Server

## 1. Purpose

This SOP provides step-by-step instructions for checking site availability, verifying Apache configuration files, and deploying applications using the Apache server with both HTTP and HTTPS.

## 2. Prerequisites

- SSH access to the server.
- Apache installed and running on the server.
- Necessary permissions to edit configuration files and restart the Apache service.
- Application files prepared and uploaded to the server directory (e.g., `/var/www/html/`).
- Domain name pointed to the server's IP address.
- Access to obtain an SSL certificate (e.g., Let’s Encrypt).

## 3. Procedure

### 3.1 Start, Enable, and Check Apache Service Status

1. **Verify Apache Service Status:**
   ```bash
   sudo systemctl start apache2
   sudo systemctl enable apache2
   sudo systemctl status apache2
   ```
2. **Check the Apache Logs for Errors:**
   ```bash
   sudo tail -f /var/log/apache2/error.log
   ```

### 3.2 Configure Apache for the Site (HTTP and HTTPS)

#### HTTP Configuration

1. **Navigate to the Apache Site Configurations Directory:**
   ```bash
   cd /etc/apache2/sites-available/
   ```
2. **Create or Edit the Virtual Host Configuration File:** (Always back up configuration files before making changes.)
   ```bash
   sudo vim /etc/apache2/sites-available/yourdomain.com.conf
   ```
   **HTTP Virtual Host Configuration:**
   ```apache
   <VirtualHost *:80>
       ServerAdmin admin@yourdomain.com
       ServerName yourdomain.com
       ServerAlias www.yourdomain.com
       DocumentRoot /var/www/html/yourdomain.com/public

       <Directory /var/www/html/yourdomain.com/public>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/yourdomain_error.log
       CustomLog ${APACHE_LOG_DIR}/yourdomain_access.log combined
   </VirtualHost>
   ```
3. **Enable the Site:**
   ```bash
   sudo a2ensite yourdomain.com.conf
   ```
4. **Reload Apache Service:**
   ```bash
   sudo systemctl reload apache2
   ```

#### HTTPS Configuration

1. **Edit the Virtual Host Configuration File for HTTPS:**
   ```bash
   sudo vim /etc/apache2/sites-available/yourdomain.com-le-ssl.conf
   ```
   **HTTPS Virtual Host Configuration:**
   ```apache
   <IfModule mod_ssl.c>
   <VirtualHost *:443>
       ServerAdmin admin@yourdomain.com
       ServerName yourdomain.com
       ServerAlias www.yourdomain.com
       DocumentRoot /var/www/html/yourdomain.com/public

       <Directory /var/www/html/yourdomain.com/public>
           Options Indexes FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       SSLEngine on
       SSLCertificateFile /etc/letsencrypt/live/yourdomain.com/fullchain.pem
       SSLCertificateKeyFile /etc/letsencrypt/live/yourdomain.com/privkey.pem

       ErrorLog ${APACHE_LOG_DIR}/yourdomain_ssl_error.log
       CustomLog ${APACHE_LOG_DIR}/yourdomain_ssl_access.log combined
   </VirtualHost>
   </IfModule>
   ```
2. **Enable the HTTPS Site:**
   ```bash
   sudo a2ensite yourdomain.com-le-ssl.conf
   ```
3. **Reload Apache Service:**
   ```bash
   sudo systemctl reload apache2
   ```

### 3.3 Verify Configuration Files

1. **Check Apache Syntax:**
   ```bash
   sudo apachectl configtest
   ```
2. **Ensure Required Modules Are Enabled:**
   ```bash
   sudo a2enmod rewrite
   sudo a2enmod headers
   sudo a2enmod ssl
   ```
   Reload Apache after enabling modules:
   ```bash
   sudo systemctl reload apache2
   ```

### 3.4 Deploy the Application

1. **Upload Application Files:** Copy the application files to the appropriate directory:
   ```bash
   sudo rsync -avz /local/path/to/application/ /var/www/html/yourdomain.com/
   ```

Or Use WINSCP

2. **Set Correct Permissions:** Ensure files and directories have the appropriate ownership and permissions:
   ```bash
   sudo chown -R www-data:www-data /var/www/html/yourdomain.com/
   sudo chmod -R 755 /var/www/html/yourdomain.com/
   ```
3. **Restart Apache:**
   ```bash
   sudo systemctl restart apache2
   ```

### 3.5 Final Checks

1. **Access the Site:** Open the site in a browser:

   - HTTP: `http://yourdomain.com`
   - HTTPS: `https://yourdomain.com`

2. **Inspect Logs:** Monitor the logs to ensure the site is running smoothly:

   ```bash
   sudo tail -f /var/log/apache2/access.log /var/log/apache2/error.log
   ```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### 1. Install Certbot and the Apache Plugin
1. **Update the package list:**
   ```bash
   sudo apt update
   ```
2. **Install Certbot and the Apache plugin:**
   ```bash
   sudo apt install certbot python3-certbot-apache
   ```

### 2. Obtain and Install SSL Certificate
1. **Run Certbot to generate the SSL certificate:**
   ```bash
   sudo certbot --apache -d yourdomain.com -d www.yourdomain.com
   ```
   - Certbot will prompt you to choose whether to redirect HTTP traffic to HTTPS. Choose the appropriate option (redirection is recommended).
   - Certbot will automatically update your Apache configuration files.

### 3. Check Certbot Auto-Renewal
1. **Verify the systemd timer for Certbot:**
   ```bash
   sudo systemctl status certbot.timer
   ```
   - The output should indicate that the timer is active and running.

2. **Test the renewal process manually:**
   ```bash
   sudo certbot renew --dry-run
   ```
   - Ensure there are no errors during the dry run.
