# Full Deployment Guide on Ubuntu 20.04

the full process of setting up MySQL and Apache with PHP on Ubuntu 20.04:

## Prerequisites

Before proceeding, ensure you have:

- A server running Ubuntu, version 18.04 or higher
- A non-root user with sudo privileges
- An active firewall

## Setting Up MySQL:

1. **Update Package Index:**

   ```bash
   sudo apt update
   ```

2. **Install MySQL Server:**

   ```bash
   sudo apt install mysql-server
   ```

3. **Configure MySQL:**

   ```bash
   sudo mysql_secure_installation
   ```

   Follow the prompts to set up a root password, remove anonymous users, disallow root login remotely, remove test databases, and reload privileges.

4. **Log into MySQL:**

   ```bash
   sudo mysql
   ```

5. **Create a Database:**

   ```sql
   CREATE DATABASE example_database;
   ```

6. **Create a New User:**

   ```sql
   CREATE USER 'example_user'@'%' IDENTIFIED BY 'password';
   ```

7. **Grant Permissions:**

   ```sql
   GRANT ALL ON example_database.* TO 'example_user'@'%';
   ```

8. **Exit MySQL:**
   ```sql
   exit;
   ```

## Setting Up Apache with PHP:

1. **Update Package Index:**

   ```bash
   sudo apt update
   ```

2. **Install Apache:**

   ```bash
   sudo apt install apache2
   ```

3. **Install PHP:**

   ```bash
   sudo apt install php libapache2-mod-php php-mysql
   ```

4. **Adjust Firewall:**
   Allow traffic on Apache:

   ```bash
   sudo ufw allow in "Apache"
   ```

5. **Change Directory Index:**
   Edit the dir.conf file:

   ```bash
   sudo nano /etc/apache2/mods-enabled/dir.conf
   ```

   Move the PHP index file to the first position:

   ```apache
   <IfModule mod_dir.c>
       DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
   </IfModule>
   ```

6. **Restart Apache:**
   ```bash
   sudo systemctl restart apache2
   ```

## Using the index.php File:

1. **Create or Edit index.php:**
   ```bash
   sudo nano /var/www/html/index.php
   ```
2. **Add PHP Content:**

   ```php
   <?php
   // Connect to MySQL
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
   ?>
   ```

3. **Save and Exit:**
   Press `CTRL + X`, type `Y`, and then press `ENTER`.

Access the PHP info page in your browser:

```
http://your_server_ip/info.php
```

We've successfully deployed a LAMP stack on your Ubuntu server. You can now host dynamic websites and web apps written in PHP.
