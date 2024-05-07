# Setting Up a LEMP Stack on Ubuntu 20.04

## Introduction

This guide demonstrates how to install a LEMP stack on an Ubuntu 20.04 server.

## Step 1 – Installing Nginx

Update your server's package index and install Nginx:

```bash
sudo apt update
sudo apt install nginx
```

If UFW firewall is enabled, allow HTTP traffic:

```bash
sudo ufw allow 'Nginx HTTP'
```

## Step 2 – Installing MySQL

Install MySQL server and run security script:

```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```

## Step 3 – Installing PHP

Install PHP-FPM and PHP-MySQL packages:

```bash
sudo apt install php-fpm php-mysql
```

## Step 4 – Configuring Nginx to Use PHP Processor

Create a server block configuration file:

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

Paste the following configuration:

```nginx
server {
    listen 80;
    server_name your_domain www.your_domain;
    root /var/www/your_domain;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }
}
```

Activate the configuration and reload Nginx:

```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
sudo unlink /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

![index.html image](https://drive.google.com/uc?export=view&id=1a8tu-m2xM1bv5-mSXokeFU-bg7c3AlxJ)
![index.html image](https://drive.google.com/uc?export=view&id=1EexLvKyjwx8bh3yRxqYzBOpRV2yjhrTA)

## Step 5 – Testing PHP with Nginx

Create a test PHP file:

```bash
sudo nano /var/www/your_domain/info.php
```

Paste the following PHP code:

```php
<?php
phpinfo();
```

Access the file in your browser:

```
http://server_domain_or_IP/info.php
```

![info.php image](https://drive.google.com/uc?export=view&id=1zqmS-X2mhJKds5zHiyTNwy4_Is2oDVDq)

## Step 6 – Testing Database Connection from PHP (Optional)

Connect to MySQL and execute queries.

```bash
sudo mysql
```

Create a new database:

```sql
CREATE DATABASE example_database;
```

Create a new user and grant permissions:

```sql
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
GRANT ALL ON example_database.* TO 'example_user'@'%';
```

Exit MySQL console:

```sql
exit
```

Create a PHP script to connect to MySQL and execute queries.

```php
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

Access the script in your browser:

```
http://server_domain_or_IP/todo_list.php
```
