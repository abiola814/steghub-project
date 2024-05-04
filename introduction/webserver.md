# Setting Up Apache Web Server for Frontend

Apache HTTP Server, commonly referred to as Apache, is a widely used open-source web server software. It's known for its reliability, flexibility, and performance.

## Installation

Apache is available in the package repositories of most Linux distributions. You can install it using your package manager.

### Debian/Ubuntu:

```bash
sudo apt update
sudo apt install apache2
```

### CentOS/RHEL:

```bash
sudo yum install httpd
```

## Basic Configuration

After installing Apache, you may need to configure it based on your requirements.

### Start/Stop Apache:

To start Apache:

```bash
sudo systemctl start apache2    # For Debian/Ubuntu
sudo systemctl start httpd      # For CentOS/RHEL
```

To stop Apache:

```bash
sudo systemctl stop apache2    # For Debian/Ubuntu
sudo systemctl stop httpd      # For CentOS/RHEL
```

### Enable Auto-Start:

To enable Apache to start automatically on boot:

```bash
sudo systemctl enable apache2    # For Debian/Ubuntu
sudo systemctl enable httpd      # For CentOS/RHEL
```

### Firewall Configuration:

If your server has a firewall enabled, you may need to allow HTTP traffic.

```bash
sudo ufw allow 'Apache'
```

### Default Document Root:

The default document root where your website files are served from is `/var/www/html/`. You can place your HTML, CSS, and other files in this directory.

## Testing Apache

After installation and configuration, you can test if Apache is running correctly by accessing your server's IP address or domain name in a web browser. You should see the default Apache welcome page.

## Virtual Hosts

Virtual hosts allow you to host multiple websites/domains on a single server.

### Creating a Virtual Host:

1. Create a new configuration file for your virtual host:

```bash
sudo nano /etc/apache2/sites-available/example.com.conf
```

2. Add the following configuration:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@example.com
    ServerName example.com
    ServerAlias www.example.com
    DocumentRoot /var/www/example.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

3. Enable the virtual host:

```bash
sudo a2ensite example.com
```

4. Reload Apache for the changes to take effect:

```bash
sudo systemctl reload apache2
```

5. Create the directory for your website files and set appropriate permissions:

```bash
sudo mkdir -p /var/www/example.com/public_html
sudo chown -R $USER:$USER /var/www/example.com/public_html
sudo chmod -R 755 /var/www/example.com
```

### Testing Virtual Host:

Upload your website files to `/var/www/example.com/public_html/` and access `http://example.com` in a web browser.

# Setting Up Apache Web Server as a Backend

Apache HTTP Server can also be used as a backend server to handle requests from frontend servers or as a reverse proxy for applications running on different ports or servers.

## Installation

The installation process for Apache remains the same as outlined in the previous guide.

### Debian/Ubuntu:

```bash
sudo apt update
sudo apt install apache2
```

### CentOS/RHEL:

```bash
sudo yum install httpd
```

## Basic Configuration

The basic configuration steps also remain the same. However, you may need to tweak the configuration based on your backend requirements.

### Start/Stop Apache:

Use the same commands to start and stop Apache as outlined in the previous guide.

### Firewall Configuration:

Allow HTTP traffic if your server has a firewall enabled.

```bash
sudo ufw allow 'Apache'
```

## Testing Apache as a Backend

After installation and configuration, you can test if Apache is running correctly by accessing your server's IP address or domain name in a web browser. You should see the default Apache welcome page.

## Reverse Proxy Configuration

Apache can act as a reverse proxy to forward requests to backend servers or applications running on different ports or servers.

### Example Configuration:

```apache
<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / http://backend-server-ip:backend-port/
    ProxyPassReverse / http://backend-server-ip:backend-port/
</VirtualHost>
```

Replace `example.com` with your domain name and `backend-server-ip` and `backend-port` with the IP address and port of your backend server or application.

```

```
