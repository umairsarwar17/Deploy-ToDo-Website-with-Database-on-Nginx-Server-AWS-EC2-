# Deploy ToDo Website with Database on Nginx Server (AWS EC2)

## Prerequisites

1. **AWS Account:** Ensure you have an active AWS account.
2. **GitHub Repository:** Use the repository at `https://github.com/ali-azgar-rakib/Todo-list-with-php.git`.
3. **Database:** The website should support a database such as MySQL or MariaDB.
4. **AWS CLI (Optional):** Install the AWS Command Line Interface for convenience.

---

## Step 1: Launch an EC2 Instance

1. **Log in to the AWS Management Console:**
   Navigate to the **EC2 Dashboard**.

2. **Launch an Instance:**
   - Click on **Launch Instance**.
   - **Choose an AMI:** Select an Amazon Machine Image (AMI), such as **Amazon Linux 2** or **Ubuntu Server**.
   - **Instance Type:** Select **t2.micro** (free tier eligible).
   - **Configure Security Group:** Allow the following inbound rules:
     - **HTTP (Port 80):** Open to `0.0.0.0/0`.
     - **SSH (Port 22):** Restrict to your IP or open to `0.0.0.0/0` (not recommended for production).
     - **MySQL/Aurora (Port 3306):** Open to `0.0.0.0/0` for database access (adjust for security).
   - **Key Pair:** Create or select an existing key pair for SSH access.
   - **Launch the Instance.**

---

## Step 2: Connect to Your EC2 Instance

1. **SSH into the Instance:**
   Use the `.pem` key file to connect:
   ```bash
   ssh -i "your-key-file.pem" ec2-user@<INSTANCE_PUBLIC_IP>
   ```

2. **Update the Package Manager:**
   Run the following commands to update the system:
   ```bash
   sudo yum update -y   # For Amazon Linux
   sudo apt update -y   # For Ubuntu
   ```

---

## Step 3: Install Nginx and Git

1. **Install Nginx:**
   ```bash
   sudo yum install nginx -y   # For Amazon Linux
   sudo apt install nginx -y   # For Ubuntu
   ```

2. **Start and Enable Nginx:**
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

3. **Install Git:**
   ```bash
   sudo yum install git -y   # For Amazon Linux
   sudo apt install git -y   # For Ubuntu
   ```

4. **Verify Nginx Installation:**
   Access the EC2 instance's public IP in your browser:
   ```
   http://<INSTANCE_PUBLIC_IP>
   ```
   You should see the default Nginx welcome page.

---

## Step 4: Clone the ToDo Website Repository

1. **Navigate to the Web Directory:**
   ```bash
   cd /usr/share/nginx/html
   ```

2. **Clone the GitHub Repository:**
   ```bash
   sudo git clone https://github.com/ali-azgar-rakib/Todo-list-with-php.git .
   ```

3. **Verify Files:**
   Ensure the website files are in the `/usr/share/nginx/html` directory:
   ```bash
   ls -l
   ```

---

## Step 5: Install and Configure Database

1. **Install MariaDB or MySQL:**
   ```bash
   sudo yum install mariadb-server -y   # For Amazon Linux
   sudo apt install mysql-server -y     # For Ubuntu
   ```

2. **Start and Enable the Database Service:**
   ```bash
   sudo systemctl start mariadb   # For MariaDB
   sudo systemctl enable mariadb
   sudo systemctl start mysql     # For MySQL
   sudo systemctl enable mysql
   ```

3. **Secure the Database Installation:**
   Run the following command and follow the prompts:
   ```bash
   sudo mysql_secure_installation
   ```

4. **Create the Database:**
   Log into the database and create a new database for the ToDo website:
   ```bash
   sudo mysql -u root -p
   CREATE DATABASE todo;
   EXIT;
   ```

5. **Import the Database Schema:**
   If your repository includes a database dump file (e.g., `schema.sql`):
   ```bash
   sudo mysql -u root -p todo < /usr/share/nginx/html/schema.sql
   ```

---

## Step 6: Configure the Website

1. **Update Configuration Files:**
   Modify the database connection settings in the website's configuration file (e.g., `config.php`). Example:
   ```php
   <?php
   $servername = "localhost";
   $username = "root";
   $password = "your_password";
   $dbname = "todo";
   ?>
   ```

2. **Set Permissions:**
   Ensure the web server can access the files:
   ```bash
   sudo chown -R nginx:nginx /usr/share/nginx/html
   sudo chmod -R 755 /usr/share/nginx/html
   ```

---

## Step 7: Verify Deployment

1. **Restart Nginx:**
   ```bash
   sudo systemctl restart nginx
   ```

2. **Access the Website:**
   Open the EC2 instance's public IP in your browser:
   ```
   http://<INSTANCE_PUBLIC_IP>
   ```

3. **Verify Functionality:**
   Ensure the website and database are working as expected.

---

## Step 8: Secure and Clean Up

1. **Enhance Security:**
   - Restrict SSH access to specific IPs in the security group.
   - Regularly update and patch the system.

2. **Backup Data:**
   Regularly back up your website files and database.

3. **Remove Unused Files:**
   Delete unnecessary files from `/usr/share/nginx/html` to keep the server clean.
