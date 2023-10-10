# AWS-EC2-LAMP-Server-for-ss-using-Amazon-Linux-2
Installing LAMP (Amazon Linux 2, Apache. MySql and PHP) on Ec2 Machine to Deploy ss App.
For Ubuntu Linux LAMP Server <a href="https://github.com/Supersilentdev/AWS-EC2-LAMP-Server-for-ss-using-Ubuntu">click hear</a>.

**Sign Up or Log In to AWS**

   - Create an AWS <a href="https://aws.amazon.com/">free-tier</a> account if you don't already have one. If you do, simply <a href="https://aws.amazon.com">log in</a> to your existing account.

**Setting Up an EC2 Instance**

   - Navigate to the EC2 service within the AWS Console.
   - Ensure that the IAM user associated with your account has the necessary EC2 permissions to avoid encountering any errors.

**Selecting Region and Availability Zone**

   - Choose the specific AWS Region according to your project's requirements.

**Instance Configuration**

   - Under the "Instances" section, select "Launch Instances."
   - Provide a meaningful name for your instance and optionally, add tags for resource organization.
   - Select the Amazon Machine Image (AMI), choose "Amazon Linux 2".
   - Choose the instance type based on your server's requirements. (For the AWS free tier, options like "t2.micro" or "t3.micro" are suitable, depending on your region.)

**Key Pair Creation**

   - Create a new key pair. This key pair represents your public key and is required for SSH access to your EC2 machine. Ensure the utmost care in protecting this key. If you already have a key, you can choose to use it.

**Networking Configuration**

   - Select the Virtual Private Cloud (VPC) and Subnet. Amazon provides default VPC and Subnet options in each region, but you can also opt for custom configurations.
   - Assign an auto-assigned IP address.

**Security Group Configuration**

   - Create a new security group or use an existing one. Ensure that this security group has SSH, HTTP, and HTTPS ports open to the public (0.0.0.0).

**Storage Configuration**

   - Configure Elastic Block Store (EBS) volumes, specifying type and size (e.g., gp2/gp3 for free tier, depending on your region).
   - Optionally, encrypt the EBS volume with a Key Management Service (KMS) key, ensuring that you have the necessary KMS permissions.

**Advanced details (User Data)** 

- Paste the following script into the user data section.
    
    ```bash
    #!/bin/bash
    sudo yum update -y
    sudo yum install httpd php mariadb-server -y
    sudo amazon-linux-extras install php7.2 -y
    sudo yum install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip -y
    sudo systemctl start httpd mariadb
    sudo wget https://ss.org/latest.tar.gz
    sudo tar -xzvf latest.tar.gz
    sudo rm latest.tar.gz
    sudo mv ss /var/www/
    sudo chown -R apache /var/www/ss
    sudo chmod -R 775 /var/www/ss
    sudo systemctl enable httpd mariadb
    ```
- Launch the Instance.

**Setting Up SSH to Access Your Server**

   - Open your preferred SSH client.

   - Locate the private key file associated with your instance, which is typically in the format KEYXXX.pem.

   - Run the following command to secure your private key(change"KEYXXX.pem" with your key name in the command).

     ```bash
     chmod 0400 KEYXXX.pem
     ```
   - Use the following SSH command to access your server (change "KEYXXX.pem" with your KEY and "3-XXX-XX-235" with your server public IP address in the command).

     ```bash
     ssh -i KEYXXX.pem ec2-user@3-XXX-XX-235
     ```

**Creating a Database for ss**

   - Create an SQL User and Set Password**

     ```bash
     sudo mysql -u root -p
     ```

   - Replace 'wpuser' and 'wppassword' with a secure username and password in the command:

     ```sql
     CREATE DATABASE ss;
     CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wppassword';
     GRANT ALL PRIVILEGES ON * . * TO 'wpuser'@'localhost';
     FLUSH PRIVILEGES;
     exit;
     ```

**Making ss default**

   - Update the httpd configuration file to set ss as the default DocumentRoot:

     ```bash
     sudo nano /etc/httpd/conf/httpd.conf
     ```

   - Change:

     ```
     DocumentRoot /var/www/html
     ```

     to:

     ```
     DocumentRoot /var/www/wordpress/
     ```

   - Restart httpd:

     ```bash
     sudo systemctl restart httpd
     ```

Done, Now Access Your ss Installation & Set it up using Server Public IP Address.(Example: http://3-XXX-XX-235 use http not https)

**Installing SSL Certificate for ss**

1. **Adding Your Website's DNS Name to the Configuration File**

   - Edit the httpd configuration file:

     ```bash
     sudo nano /etc/httpd/conf/httpd.conf
     ```

   - Add your website's DNS name by pasting the following at the end of file and replace ServerAdmin, ServerName, ServerAlias with your values:

     ```
      <VirtualHost *:80>
      ServerName myxxxxxx.com
      DocumentRoot /var/www/wordpress
      </VirtualHost>
     ```
   - Restart httpd:

     ```bash
     sudo systemctl restart httpd
     ```
2. **Installing SSL/TLS Certificate Using Let's Encrypt Certbot**

   - Install Epel, Certbot and the Certbot Apache plugin:

     ```bash
     sudo amazon-linux-extras install epel -y
     ```
     ```bash
     sudo yum install python2-certbot-apache -y
     ```
       
   - Run Certbot to obtain and configure the SSL certificate:

     ```bash
     sudo certbot --apache -d myxxxxxxxx.com
     ```

   - Follow the prompts and provide your email.

Congratulations! Your ss website is now set up securely with SSL/TLS encryption. Enjoy your website.
