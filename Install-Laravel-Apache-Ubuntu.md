### Step 1: Prepare Your Droplet
- Connect to your droplet via SSH:
```sh
ssh root@your-droplet-ip
```
- Update and upgrade your system:
```sh
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Apache
```sh
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl status apache2
```
### Step 3: Install PHP and Dependencies
- Add the required PHP repository:
```sh
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```
- Install PHP and dependencies:
```sh
sudo apt install php libapache2-mod-php php-cli php-mysql php-curl php-json php-mbstring php-xml php-zip unzip -y
```
- Verify the PHP version:
```sh
php -v
```

### Step 4: Install Composer
- Download and install Composer:
```sh
sudo apt install curl -y
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

- Verify Composer installation:
```sh
composer -v
```
### Step 5: Install Laravel
- Navigate to the web root directory:
```sh
cd /var/www/html
```
- Create a Laravel project:
```sh
composer create-project --prefer-dist laravel/laravel laravel-app
```

- Set permissions for the Laravel directory:
```sh
sudo chown -R www-data:www-data /var/www/html/laravel-app
sudo chmod -R 775 /var/www/html/laravel-app/storage /var/www/html/laravel-app/bootstrap/cache
```

### Step 6: Configure Apache for Laravel
- Create a new Apache virtual host configuration file:
```sh
sudo vim /etc/apache2/sites-available/laravel-app.conf
```
- Add the following configuration:
```sh
<VirtualHost *:80>
    ServerName your-domain.com                       //or public ip of droplet
    DocumentRoot /var/www/html/laravel-app/public

    <Directory /var/www/html/laravel-app/public>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Enable the new configuration and mod_rewrite:
```sh
sudo a2ensite laravel-app
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 7: Update .env for Database Connection
- Open the .env file:
```sh
vim /var/www/html/laravel-app/.env
```

- Update the database details:
```sh
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=Redhat2040!
```

### Step 8: Install MySQL 
- Install MySQL:
```sh
sudo apt install mysql-server -y
```
- Secure MySQL:
```sh
sudo mysql_secure_installation
```
- Log in to MySQL and create a database:
```sh
mysql -u root -p
```
```sql
CREATE DATABASE laravel;
```
- Change the authentication plugin for the root user:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Redhat2040!';
```
- Apply changes:
```sql
FLUSH PRIVILEGES;
EXIT;
```
- Run migrations to ensure Laravel can connect to the database
```sh
cd /var/www/html/laravel-app && php artisan migrate
```

### Step 9: Test Laravel
- Navigate to your domain or droplet IP:
```sh
http://your-domain.com
```
You should see the Laravel welcome page.


### Step 10: Create a Simple Laravel Project
#### Generate a Controller and Route
- Create a controller:
```sh
php artisan make:controller HelloController
```
- Open the controller file and add a method:
```sh
// /var/www/html/laravel-app/app/Http/Controllers/HelloController.php
// Incide the class HelloController
public function greet()
{
    return "Hello, DigitalOcean!";
}
```

- Add a route in routes/web.php:
```sh
Route::get('/hello', [App\Http\Controllers\HelloController::class, 'greet']);
```

- Test the Route
Navigate to:
```sh
http://your-domain.com/hello
```
You should see the message "Hello, DigitalOcean!".
