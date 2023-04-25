#     WEB STACK IMPLEMENTATION (LEMP STACK)
 After successfully implementing LAMP stack, In this project we will be implementing LEMP stack but with an alternative Web Server – NGINX, which is also very popular and widely used by many websites in the Internet. As we have done in project 1, we’ll be creating a new instance for project 2 and ssh into the instance.
###  INSTALLING THE NGINX WEB SERVER
   We will need to install NGINX in other to display web pages to site visitors and NGINX is a high-performance web server. Since this is our first time using apt [ADVANCED PACKAGE TOOL] for this project, we will start by updating the server’s package index. Following that, we’ll use apt install to get Nginx installed:

#1 "sudo apt update"
"sudo apt install nginx"
      After entering the command, enter Y to confirm that you want to install Nginx. Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

#2 To verify that nginx was successfully installed and is running as a service in Ubuntu, run "sudo systemctl status nginx"

 If it is green and running, then everything was done  correctly. We've launched NGINX in the clouds. In other to start receiving traffic, we need to open TCP Port 80 which is the default port used by web browsers to access web pages. As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80. Once done the server will be running and we can access it locally in our Ubuntu shell by running this command 'curl http://localhost:80 Or curl http://127.0.0.1:80'
 
  Now let's open our browser and try accessing our web server with the following url http://<Public-IP-Address>:80  you can also access the page using your DNS name as well
  ![unknown](https://user-images.githubusercontent.com/127052041/234406236-f25a7545-64ec-43b4-96d4-95992aefe89e.png)
 ![sudo systemctl status nginx](https://user-images.githubusercontent.com/127052041/234406422-cc20d81b-1ba1-4f96-8684-6a98ab353d4a.png)
![curl httplocalhost80](https://user-images.githubusercontent.com/127052041/234406540-f73250c8-e7fa-4448-969b-3521fe8523a2.png)
![Welcome to nginx!](https://user-images.githubusercontent.com/127052041/234406646-1360fdde-3ba2-465b-b86d-8d9696e5e83a.png)

##  INSTALLING MYSQL 
  
    NGINX web server is up and running now we will need to install a Database Management System (DBMS ) to be able to store and manage data for your site in a relational database so this is where MYSQL will come into play.
  
#1 Again, use ‘apt’ to acquire and install this software 
'sudo apt install mysql-server'

 #2 log in to the MySQL console using this command 
'sudo mysql'
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command.

#3 It’s recommended that we  run a security script that comes pre-installed with  MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script we will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1 
'ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';'
 
  #4 Exit the MYSQL shell with 
'mysql> exit'
  
  #5 Start the interactive script by running
'sudo mysql_secure_installation'
This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Answer Y for yes, or anything else to continue without enabling. If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words e.g PassWord.1

  Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure. We’ll talk about this in a moment.

  If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt. For the rest of the questions, press Y and hit the ENTER key at each prompt.

  #6 When you’re finished, test if you’re able to log in to the MySQL console with this command  'sudo mysql -p'
  The -p flag in this command, which will prompt you for the password used after changing the root user password in this case  PassWord.1 

  #7 To exit the MySQL console 'mysql> exit'
MySQL server is now installed and secured.
![unknown](https://user-images.githubusercontent.com/127052041/234408088-6ba6d75c-b1ef-49bd-9b71-733be45cdc40.png)
  ![unknown](https://user-images.githubusercontent.com/127052041/234408171-37c87943-03de-4712-bbf4-dc6af1a36e25.png)
  ![Enter password](https://user-images.githubusercontent.com/127052041/234408267-2e5a06e5-c17e-49fa-8650-b4a538c7456c.png)

  ##     INSTALLING PHP
  
  Nginx has been installed to serve your content and MySQL installed to store and manage your data. Now we can install PHP to process code and generate dynamic content for the web server.  

    While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

  
To install these 2 packages at once run 'sudo apt install php-fpm php-mysql'
  
  When prompted, type Y and press ENTER to confirm installation.

  Now PHP components has been installed.
  ![unknown](https://user-images.githubusercontent.com/127052041/234408654-9ad612d3-6306-40b9-8fad-b0f4a191828e.png)
  
  ## CONFIGURING NGINX TO USE PHP PROCESSOR
  
  When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. we will use piusprojectLEMP as domain name.

  On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

  #1 Create the root web directory for your_domain
'sudo mkdir /var/www/projectLEMP'
  
  #2 Change ownership of the directory with the $USER, which is your current system user. 'sudo chown -R $USER:$USER /var/www/piusprojectLEMP'
  
  #3 open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano 'sudo nano /etc/nginx/sites-available/piusprojectLEMP'
  
  #4 This will create a new blank file. Paste in the following configuration 
#/etc/nginx/sites-available/piusprojectLEMP


server {
    listen 80;
    server_name piusprojectLEMP www.piusprojectLEMP;
    root /var/www/piusprojectLEMP;


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
  
When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

#5 Activate your configuration by linking to the config file from Nginx’s sites-enabled directory: 'sudo ln -s /etc/nginx/sites-available/piusprojectLEMP /etc/nginx/sites-enabled/'
  
  #6 This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors 'sudo nginx -t'
  
  If any errors are reported, go back to your configuration file to review its contents before continuing. If you see the following message then you are good to go. 

  #nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

  #7 Now disable default Nginx host that is currently configured to listen on port 80, for this run 'sudo unlink /etc/nginx/sites-enabled/default'
  
  #8 Reload Nginx to apply the changes 'sudo systemctl reload nginx'
  
  #9 The website is now active, but the web root /var/www/piusprojectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected. 'sudo echo 'Hello piusLEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/piusprojectLEMP/index.html '
  
   Go to your browser and use your IP or DNS name, if you get what you pasted after “echo” then you did everything correctly. 
![unknown](https://user-images.githubusercontent.com/127052041/234410150-2a8225a8-6c86-427d-a89f-56c8a3b0e1dc.png)
  ![ubuntu@ip-172-31-30-20-$ sudo nginx -t](https://user-images.githubusercontent.com/127052041/234410216-55799168-0617-4e4d-bc47-726f2183fa79.png)
  ![index html](https://user-images.githubusercontent.com/127052041/234410332-33fb963b-65e9-4526-80fe-befcc93ce20d.png
  ![unknown](https://user-images.githubusercontent.com/127052041/234410400-c4cbb409-e037-4cf7-9d4e-7c514a43bf93.png)
  
  ## TESTING PHP WITH NGINX 

    Lets  validate that Nginx can correctly hand .php files off to your PHP processor. Lets create a test PHP file in document root.

  #1 Open a new file called info.php within document root in your text editor: 'sudo nano /var/www/piusprojectLEMP/info.php' 
  
  #2 Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:
 '<?php
phpinfo();'

You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php. http://`server_domain_or_IP`/info.php

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file: 'sudo rm /var/www/piusprojectLEMP/info.php'
![php](https://user-images.githubusercontent.com/127052041/234410872-2ed4e4cf-2907-4d9e-8be8-60b0cc2cb0c5.png)

## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

Lets create a test database (DB) with "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it. We’ll create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP. 
We will create a database named example_database and a user named example_user, but it can replaced  with different values/names.

#1 Connect to the MYSQL consol using the root user account
      'sudo mysql -p'

#2 To create a new database, run the following command from MySQL console 'mysql> CREATE DATABASE `example_database`;  '

#3 create a new user and grant him full privileges on the database you have just created. The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1, but you should replace this value with a secure password of your own choosing. 'mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';  '

#4 Now we need to give this user permission over the example_database database: 'mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';  '

This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server

#5 Now exit the MySQL shell with: 'mysql> exit'

#6 Lets test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials: 'mysql -u example_user -p '

#7 After logging in to the MySQL console, confirm that you have access to the example_database database: 'mysql> SHOW DATABASES;  '

This will give you the following output:

Output
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement 

CREATE TABLE example_database.todo_list (

mysql>     item_id INT AUTO_INCREMENT,

mysql>     content VARCHAR(255),

mysql>     PRIMARY KEY(item_id)

mysql> );

#9 Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:' mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");  '

'mysql> INSERT INTO example_database.todo_list (content) VALUES ("Pius is a KIng");  '

#10 To confirm that the data was successfully saved to your table, run:' mysql>  SELECT * FROM example_database.todo_list; ''

#11 After confirming that you have valid data in your test table, you can exit the MySQL console: 'mysql> exit ''

#12 Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:' vi /var/www/piusprojectLEMP/todo_list.php '''

#13 The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.
Copy this content into your todo_list.php script: 

'' <?php
$user = "example_user";
$password = "PassWord.1";
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
'''
Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

http ://<Public_domain_or_IP>/todo_list.php

If you see your to do list content that means your PHP environment is ready to connect and interact with your MySQL server. 
![Enter password](https://user-images.githubusercontent.com/127052041/234412595-b697e692-d4b2-4354-95ec-ef94d222cd9a.png)

![Oracle is a registered trademark of Oracle Corporation andor its](https://user-images.githubusercontent.com/127052041/234412660-3af5a28f-63aa-4da8-861a-a39d78503a43.png)
![unknown](https://user-images.githubusercontent.com/127052041/234412733-cc137b7f-e306-49e9-8839-d9cb15d51ff7.png)
![iten id  content](https://user-images.githubusercontent.com/127052041/234412783-a725ae46-e0a3-4dee-812c-a064f2bcc7ee.png)
![GNU nano 6 2](https://user-images.githubusercontent.com/127052041/234412844-df56dfb6-881c-4f97-95f4-5faa817a8c97.png)
![unknown](https://user-images.githubusercontent.com/127052041/234412975-2257a187-783c-4290-aaa7-978ad227fe79.png)






























