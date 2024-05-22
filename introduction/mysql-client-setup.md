## TASK-Implement a Client Server Architecture using MySQL Database Management System (DBMS).


### Step 1: Create and Configure Two Ubuntu-Based EC2 Instances

1. **Launch two EC2 instances** in the same region and availability zone on AWS with Ubuntu 20.04 LTS as the operating system.
   - Name the first instance `mysql-server`.
   - Name the second instance `mysql-client`.
![index.html image](https://drive.google.com/uc?export=view&id=1fuuIunxGbnNRym2T0Vys7rbH2dKLQIhi)

2. **Security Groups Configuration**:
   - For `mysql-server`, create a security group that allows inbound traffic on port 3306 from the `mysql-client` instance's IP address.
   - For `mysql-client`, create a security group that allows outbound traffic.

### Step 2: Install MySQL Server on `mysql-server`

1. **Connect to the `mysql-server` instance** via SSH.
   ```sh
   ssh -i /path/to/your-key.pem ubuntu@<mysql-server-public-ip>
   ```

2. **Update package index**:
   ```sh
   sudo apt update
   ```

3. **Install MySQL Server**:
   ```sh
   sudo apt install -y mysql-server
   ```
![index.html image](https://drive.google.com/uc?export=view&id=1ojrmOedeVGS-Ckrk_6V5qpTF49tdFoCm)
4. **Start MySQL Service**:
   ```sh
   sudo systemctl start mysql
   sudo systemctl enable mysql
   ```
![index.html image](https://drive.google.com/uc?export=view&id=1zbFYV4OYVNC0Irs7LU8s_qXZ35Mp6fGW)
### Step 3: Configure MySQL Server for Remote Access

1. **Open MySQL configuration file**:
   ```sh
   sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
   ```
   ![index.html image](https://drive.google.com/uc?export=view&id=1Y7rhPWpd8r4wqo3ca3y2QeNlaomLnOlQ)

2. **Update the bind-address**:
   - Find the line `bind-address = 127.0.0.1` and change it to `bind-address = 0.0.0.0`.
   ![index.html image](https://drive.google.com/uc?export=view&id=1HjfvpXNRGRBkBgh5Aytwb5KPHU9mQchM)

3. **Restart MySQL Service**:
   ```sh
   sudo systemctl restart mysql
   ```
   ![index.html image](https://drive.google.com/uc?export=view&id=)

4. **Set up MySQL root password**:
   ```sh
   sudo mysql_secure_installation
   ```
   ![index.html image](https://drive.google.com/uc?export=view&id=12NsiquXdh-BjDre-nmDOBCwoZQB1ZPFO)

   ![index.html image](https://drive.google.com/uc?export=view&id=1aPzdqYLRFGRGqfNhgRVlRfRcAW-jfvvV)

5. **Allow remote connections for MySQL user**:
   - Log in to MySQL shell:
     ```sh
     sudo mysql -u root -p
     ```


   - Run the following SQL commands to create a new user or update an existing user to allow remote access:
     ```sql
     CREATE USER 'remote_user'@'%' IDENTIFIED BY 'password';
     GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'%' WITH GRANT OPTION;
     FLUSH PRIVILEGES;
    ```
    ![index.html image](https://drive.google.com/uc?export=view&id=1KrScyKNExT4EHItGRZ2u7aBA3The2Ggo)


### Step 4: Install MySQL Client on `mysql-client`

1. **Connect to the `mysql-client` instance** via SSH.
   ```sh
   ssh -i /path/to/your-key.pem ubuntu@<mysql-client-public-ip>
   ```

2. **Update package index**:
   ```sh
   sudo apt update
   ```

3. **Install MySQL Client**:
   ```sh
   sudo apt install -y mysql-client
   ```
   ![index.html image](https://drive.google.com/uc?export=view&id=1rBkXx4Zs_IjBq4uJhFOy7YIIsrX--Mzg)

### Step 5: Connect to MySQL Server from MySQL Client

1. **Find the private IP address of `mysql-server`**:
   ```sh
   hostname -I
   ```
![index.html image](https://drive.google.com/uc?export=view&id=1CqpogMsi_EWlLc7gpPdE-l0hnFyYzppA)

2. **Connect using MySQL client**:
   ```sh
   mysql -h <mysql-server-private-ip> -u remote_user -p
   ```
![index.html image](https://drive.google.com/uc?export=view&id=1XLN1whM-dFN8EvQA8swQXeSd9NiqCnEt)
### Step 6: Verify the Connection and Perform SQL Queries

1. **Check databases**:
   ```sql
   SHOW DATABASES;
   ```
![index.html image](https://drive.google.com/uc?export=view&id=1gJ6R3oOBDKmNQUyuS-ABWX3Vrky0JPHT)

If the connection is successful, you will see an output like this:

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

