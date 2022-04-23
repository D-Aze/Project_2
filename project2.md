# Documentation of Project 2

## Step 1: Installing the Nginx Web Server
- Update server’s package index with `sudo apt update`
- Get Nginx installed with `sudo apt install nginx`
- To verify that nginx was successfully installed and is running as a service in Ubuntu, run `sudo systemctl status nginx`
![nginx-status-running](./images/nginx-status-running.PNG)
- To check how to access the server locally in our Ubuntu shell, run `curl http://localhost:80` or `curl http://127.0.0.1:80`
![nginx-server-access](./images/nginx-server-access.PNG)
- To test how our Nginx server can respond to requests from the Internet, open a web browser of your choice and try to access http://< Public-IP-Address >:80
![nginx-installed](./images/nginx-installed.PNG)
(Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`)

## Step 2: Installing MySQL
- Use ‘apt’ to acquire and install this software via `sudo apt install mysql-server`
- Run security script that comes pre-installed with MySQL by running `sudo mysql_secure_installation`
- Test if you’re able to log in to the MySQL console by typing `sudo mysql`
![mysql-status](./images/mysql-status.PNG)
- To exit the MySQL console, type `exit`

## Step 3: Installing PHP
- To install the PHP package along with php-fpm (which stands for “PHP fastCGI process manager”), and tell Nginx to pass PHP requests to this software for processing, as well as and php-mysql (a PHP module that allows PHP to communicate with MySQL-based databases), run `sudo apt install php-fpm php-mysql`
- Once the installation is finished, confirm PHP version using `php -v`
![php-status](./images/php-status.PNG)
- LAMP stack is now completely installed and fully operational.

## Step 4: Configuring Nginx to Use PHP Processor
- Create the root web directory for your_domain via `sudo mkdir /var/www/projectLEMP`
- Assign ownership of the directory with the $USER environment variable, which will reference your current system user: `sudo chown -R $USER:$USER /var/www/projectLEMP`
- Open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. We’ll use nano: `sudo nano /etc/nginx/sites-available/projectLEMP`
- This will create a new blank file. Paste in the following bare-bones configuration:

#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
- When you’re done editing, save and close the file by typing CTRL+X and then y and ENTER to confirm.
- Activate your configuration by linking to the config file from Nginx’s sites-enabled directory via `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
- Test your configuration for syntax errors, using `sudo nginx -t`
![nginx-config-status](./images/nginx-config-status.PNG)
- To disable default Nginx host that is currently configured to listen on port 80, run `sudo unlink /etc/nginx/sites-enabled/default`
- Reload Nginx to apply the changes via `sudo systemctl reload nginx`
- New website is now active, but web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected, using `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`
- Go to browser and try to open website URL using IP address via http://< Public-IP-Address >:80
![hello-lemp](./images/hello-lemp.PNG)
- LEMP stack is now completely set up.

## Step 5: Testing PHP with Nginx
- Test LEMP stack to validate that Nginx can correctly hand .php files off to PHP processor. Do this by creating a test PHP file in your document root. Open a new file called info.php within your document root in your text editor, using `sudo nano /var/www/projectLEMP/info.php`
- Access this page in web browser by visiting the domain name or public IP address set up in Nginx configuration file, followed by /info.php, i.e., http://< server_domain_or_IP >/info.php
![php-enabled](./images/php-enabled.PNG)
- After checking the relevant information about PHP server through that page, remove the file created as it contains sensitive information about PHP environment and Ubuntu server. Use rm to remove it via `sudo rm /var/www/your_domain/info.php`

## Step 6: Retrieving Data from MySQL Database with PHP
- Connect to MySQL console using the root account via `sudo mysql`
- To create a new database, run the following command from your MySQL console: `CREATE DATABASE `example_database`;`
- The following command creates a new user named example_user, using mysql_native_password as default authentication method. Define this user’s password by replacing "password" with a secure password of your own choosing: `CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`
- Give this user permission over the example_database database, using `GRANT ALL ON example_database.* TO 'example_user'@'%';`
- Exit the MySQL shell with `exit`
- Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials: `mysql -u example_user -p`
- After logging in to the MySQL console, confirm access to the example_database database, using `SHOW DATABASES;`
![mysql-databases](./images/mysql-databases.PNG)
- To create a test table named todo_list from the MySQL console, run the following statement: `CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));`
- Insert a few rows of content in the test table. Repeat the next command a few times, using different VALUES: `mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`
- To confirm that the data was successfully saved to the table, run: `mysql>  SELECT * FROM example_database.todo_list;`
![to-do-list](./images/to-do-list.PNG)
- After confirming valid data in test table, exit the MySQL console via `exit`
- Create a PHP script that will connect to MySQL and query for content. Create a new PHP file in custom web root directory using preferred editor. We’ll use vi: `nano /var/www/projectLEMP/todo_list.php`
- Type this content into your todo_list.php script:
![to-do-list-script](./images/to-do-list-script.PNG)
Save and close the file when done editing.
- Access this page in web browser by visiting the domain name or public IP address configured for website, followed by /todo_list.php, i.e., http://< Public_domain_or_IP >/todo_list.php
![to-do-on-web](./images/to-do-on-web.PNG)
- PHP environment is ready to connect and interact with MySQL server.

Congratulations on building a flexible foundation for serving PHP websites and applications to visitors, using Nginx as web server and MySQL as database management system!