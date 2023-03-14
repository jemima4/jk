# WEB STACK IMPLEMENTATION (LEMP STACK) 

This is a project which implement the use of Web Stack (LEMP STACK) In AWS. LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)

## INSTALLING THE NGINX WEB SERVER 
we update the server package index with the command

> sudo apt update<br>
sudo apt install nginx

we use the command to then get NGINX installed

> sudo systemctl status nginx

we try to access it locally on our ubuntu shell: 

>curl http://localhost:80 
or 
curl http://127.0.0.1:80 

<img width="639" alt="statusnginx" src="https://user-images.githubusercontent.com/30122596/225105234-81c355ba-8529-44c0-ae55-585b181ff312.png">


-----------------------
You can retrieve your IP address uisng the command
>curl -s http://169.254.169.254/latest/meta-data/public-ipv4

Go to a web browser and open the page using the IP address

> http://<Public-IP-Address>:80

<img width="1280" alt="welcometonginx" src="https://user-images.githubusercontent.com/30122596/225105092-7bf269cc-ef6c-4c91-8f96-45d1be7644a9.png">


-------------------------------------

## INSTALLING MYSQL

 When prompted, confirm installation by typing Y, and then ENTER.
  > sudo apt install mysql-server
  
  -------------------------------
  Log in to mysql after installation 
  this will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running thus command.
  > sudo mysql
  
  <img width="639" alt="sudomysql" src="https://user-images.githubusercontent.com/30122596/225105000-12c619ed-0198-4bf9-8613-ce19b7735dc1.png">

 
 
  
  --------------------------
  Set a password for the root user using mysql_native_password as default authentication method 
  > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '< password >';


  ### Exit mysql
  > exit<br>

  it's recommend that you run a security script that comes pre-installed with MYSQL. This script will remove some insecure default settings and lock down access to your database system.
  > sudo mysql_secure_installation
  
  <img width="1280" alt="password" src="https://user-images.githubusercontent.com/30122596/225104929-a4460767-ae72-4dd4-983e-8975f12b4ad6.png">

  

 This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.
Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

----------------------------------
## Confirm Mysql was successfully installed by logging into the console
> sudo mysql -p

<img width="639" alt="sudomysql-p" src="https://user-images.githubusercontent.com/30122596/225104833-bfb6f23c-d22b-4add-92fc-2acde3f05ea0.png">



### Exit mysql
> exit 

--------------------------------
## INSTALLING PHP
You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases.You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

> sudo apt install php-fpm php-mysql 

-------------------
## CONFIGURING NGINX TO USE PHP PROCESSOR

Create the root web directory for your domain as follows: 
>sudo mkdir /var/www/projectLEMP 

 We assign ownership of the directory with the $USER environment variable, which will reference your current system user:
 >sudo chown -R $USER:$USER /var/www/projectLEMP 

open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano: 
 >sudo nano /etc/nginx/sites-available/projectLEMP  

 Copy and paste the command into the open window
 


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

Here’s what each of these directives and location blocks do:

listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.

root — Defines the document root where the files served by this website are stored.

index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.

server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.

location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.

location ~ .php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

location ~ /.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.
When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

----------------------------------

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory using the command below
> sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/ 

This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:
>sudo nginx -t 

<img width="723" alt="projectLEMP" src="https://user-images.githubusercontent.com/30122596/225104661-ba30eaa9-beff-4c28-948f-b6649fc67e3c.png">


To disable default Nginx host that is currently configured to listen on port 80 we use the command
>sudo unlink /etc/nginx/sites-enabled/default 

We reload Nginx to apply the changes:
>sudo systemctl reload nginx 

Create an index.html file in that location so that we can test that your new server block works as expected
>sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' 
$(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html 

Now go to your browser and try to open your website URL using IP address
>http://<Public-IP-Address>:80 

<img width="1280" alt="projectLEMP2" src="https://user-images.githubusercontent.com/30122596/225104567-ad1f0db1-d67b-4693-b15f-05451f81f216.png">


---------------------------------------
## TESTING PHP WITH NGINX
Create a test PHP file in your document root
>sudo nano /var/www/projectLEMP/info.php 

Copy and paste the command into the file that opens
> /<?php<br>
phpinfo(); 

You can now access this page in your web browser
>http://`server_domain_or_IP`/info.php 
<img width="1280" alt="infophp" src="https://user-images.githubusercontent.com/30122596/225104461-bbb8362d-23fe-4508-b130-04a027b097b0.png">



Remove the file using the following command
>sudo rm /var/www/projectLEMP/info.php 

----------------------------------
## RETRIEVING DATA FROM MYSQL DATABASE WITH PHP (CONTINUED)

First, connect to the MySQL console using the root account: 
>sudo mysql 

Create a new database, run the following command from your MySQL console:
>mysql> CREATE DATABASE `example_database`; 

We creates a new user named example_user, using mysql_native_password as default authentication method

>CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

Now we need to give this user permission over the example_database database:
>GRANT ALL ON example_database.* TO 'example_user'@'%';

Now exit the MySQL shell with
>exit
<img width="1280" alt="mysqldata" src="https://user-images.githubusercontent.com/30122596/225104356-02a3a332-f7d0-4ea7-8299-18dd6a406e3f.png">



------------------------------------
You can test if the new user has the proper permissions by logging in to the MySQL console again
>mysql -u example_user -p 

<img width="555" alt="mysql-u" src="https://user-images.githubusercontent.com/30122596/225104274-8f46c87f-e418-45a7-960c-e24ab00eb29a.png">


After logging in to the MySQL console, confirm that you have access to the example_database database
>SHOW DATABASES; 

This will give you the following output: 

<img width="555" alt="SHOWDATA" src="https://user-images.githubusercontent.com/30122596/225104208-b655d1d3-9427-444d-be48-1717f644b249.png">


--------------------------------
Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement

>CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

>INSERT INTO example_database.todo_list (content) VALUES ("My first important item");

<img width="867" alt="todolist" src="https://user-images.githubusercontent.com/30122596/225104139-61352d8d-3f9c-497d-9eb3-8bdacc6e2690.png">


-----------------------------
To confirm that the data was successfully saved to your table, run:
>SELECT * FROM example_database.todo_list;

This output will be shown

<img width="662" alt="todolistoutput" src="https://user-images.githubusercontent.com/30122596/225104061-d435a05b-8271-4718-939c-2782be3bcb66.png">


----------------------------
You can exit the MySQL console after confirming valid data in your test table
>exit

Now you can create a PHP script that will connect to MySQL and query for your content
> nano /var/www/projectLEMP/todo_list.php

Copy this content into your todo_list.php script:

/<?php 

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

  echo "</ol>"; <br>

} <br>
catch (PDOException $e) { 

print "Error!: " . $e->getMessage() . "<br/>"; 

die(); 
<br>
} 
<br>
-----------------------------------

Save and close the file when you are done editing.


You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php
>http://<Public_domain_or_IP>/todo_list.php



You should see a page like this, showing the content you’ve inserted in your test table

<img width="654" alt="output" src="https://user-images.githubusercontent.com/30122596/225103834-315a6469-d740-43a1-80e4-cb14d19d81e5.png">


----------------------------------------
That means your PHP environment is ready to connect and interact with your MySQL server.# jk
