### LEMP STACK DEPLOYMENT 
##### LEMP - LINUX NGINX MYSQL PHP


Launch an EC2 instance on AWS on ubuntu server of your choice (here, i used 20.04)
<img width="608" alt="Screenshot 2022-10-03 at 09 35 45" src="https://user-images.githubusercontent.com/111234300/193580631-ced9fa62-f1f6-44ca-ad8b-545701285fac.png">

Then, SSH into the instance
<img width="951" alt="Screenshot 2022-10-03 at 09 42 01" src="https://user-images.githubusercontent.com/111234300/193581153-3badf202-1db7-44f1-b97b-7eed3de15513.png">

After we have successfully connected to our ubuntu server via out local terminal
<img width="271" alt="Screenshot 2022-10-03 at 09 47 06" src="https://user-images.githubusercontent.com/111234300/193581946-e9c9a3dd-b605-4435-8a7d-dba81752975b.png">

#### STEP 1
Now, we install the NGINX web server
`sudo apt update`

`sudo apt install nginx`

next, we verify our installation with this code
`sudo systemctl status nginx`

If it's active and running, then we are good to go! 

<img width="916" alt="Screenshot 2022-10-03 at 10 00 07" src="https://user-images.githubusercontent.com/111234300/193582816-b7009df3-52c2-4279-8ff2-2401a2e4ed74.png">

Before we can receive any traffic by our Web Server, we need to open TCP port 80.
So, we need to add a rule to EC2 configuration to open inbound connection through port 80:

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

`curl http://localhost:80
or
curl http://127.0.0.1:80`


<img width="721" alt="Screenshot 2022-10-03 at 10 03 04" src="https://user-images.githubusercontent.com/111234300/193583281-6d5ced06-7ead-428a-b697-b9580776c9ac.png">

and also via web
<img width="1440" alt="Screenshot 2022-10-03 at 10 09 49" src="https://user-images.githubusercontent.com/111234300/193583394-cc5b2e5a-3669-4f9f-af71-59017a4c50b8.png">

#### STEP 2
We install MYSQL

`sudo apt install mysql-server`
After installation, we LOG into MYSQL console: `$ sudo mysql`

It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using `mysql_native_password` as default authentication method. We’re defining this user’s password as password.
NOTE: you can use any password of your choice but a strong one is advised.

<img width="757" alt="Screenshot 2022-10-03 at 10 15 22" src="https://user-images.githubusercontent.com/111234300/193584542-67845f45-c140-44be-ac82-65a651e9a1fb.png">

then, exit:
`mysql> exit`

`$ sudo mysql_secure_installation`

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

`Answer Y for yes, or anything else to continue without enabling.`

**(FOR THIS PROJECT, I DID NOT CHOOSE THE VALIDATE OPTION)**

`VALIDATE PASSWORD PLUGIN can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD plugin?`

Press y|Y for Yes, any other key for No:
If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words e.g password.

`There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1COPY`

Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure. We’ll talk about this in a moment.

If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

`Estimated strength of the password: 100 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
For the rest of the questions, press Y and hit the ENTER key at each prompt. This will prompt you to change the root password, remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.
`

When you’re finished, test if you’re able to log in to the MySQL console by typing:

<img width="744" alt="Screenshot 2022-10-03 at 10 18 28" src="https://user-images.githubusercontent.com/111234300/193587751-2293fca0-544b-4331-a9aa-67a2a668e912.png">
<img width="679" alt="Screenshot 2022-10-03 at 10 19 00" src="https://user-images.githubusercontent.com/111234300/193587874-c8388e7f-f8cb-4a9c-be61-c19a8f55f765.png">

`$ sudo mysql -p`
Notice the -p flag in this command, which will prompt you for the password used after changing the root user password.

To exit the MySQL console, type:

`mysql> exit`

Our MySQL server is now installed and secured. Next, we will install PHP, the final component in the LEMP stack.

#### STEP 3 
INSTALLING PHP

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, run:

`sudo apt install php-fpm php-mysql -y`

<img width="621" alt="Screenshot 2022-10-03 at 10 33 34" src="https://user-images.githubusercontent.com/111234300/193589970-64116f88-f664-42ee-a2eb-50bbca0581d7.png">


We now have our PHP components installed. Next, you will configure Nginx to use them.

FOR THIS DEPLOYMENT I USED PHP 8.1
So to avoid any issues with our web solution, we will be running these codes to enure everything is up-to-date and all extension (including driver packages) are available
`sudo apt update
sudo apt upgrade`

Add PPA for PHP 8.1
Add the ondrej/php which has PHP 8.1 package and other required PHP extensions.
`sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update`

Install PHP 8.1 FPM for Nginx
For Nginx you need to install FPM. Execute the following command to install PHP 8.1 FPM
`sudo apt install php8.1-fpm`

After the installation has completed, confirm that PHP 8.1 FPM has installed correctly with this command
`php-fpm8.1 -v`

<img width="589" alt="Screenshot 2022-10-03 at 14 38 46" src="https://user-images.githubusercontent.com/111234300/193591729-3b507fab-45da-4471-a687-5c4abfc7c535.png">

Install PHP 8.1 Extensions
Install some commonly used php-extensions with the following command.
`sudo apt install php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-redis php8.1-intl -y`

#### STEP 4
CONFIGURING NGINX TO USE PHP PROCESSOR

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at `/var/www/html`.
 While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying `/var/www/html`, we’ll create a directory structure within 
/var/www
 for the your_domain website, leaving 
/var/www/html
 in place as the default, directory to be served if a client request does not match any other sites.


Create the root web directory for your_domain as follows:
`sudo mkdir /var/www/projectLEMP`

Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
`sudo chown -R $USER:$USER /var/www/projectLEMP`

Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use 
vi
`sudo vi /etc/nginx/sites-available/projectLEMP`


This will create a new blank file. Paste in the following bare-bones configuration:

<img width="567" alt="Screenshot 2022-10-03 at 10 50 20" src="https://user-images.githubusercontent.com/111234300/193595177-9f7b24cd-f7ce-408f-a567-b5510aa3442e.png">

Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

`sudo nginx -t`

<img width="867" alt="Screenshot 2022-10-03 at 10 55 27" src="https://user-images.githubusercontent.com/111234300/193595774-4eb5bab5-6739-4381-bab2-aa5b851e51d6.png">


We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

Then, we reload Nginx to apply the changes:

`sudo systemctl reload nginx`

Our new website is now active, but the web root /var/www/projectLEMP is still empty. 
So, create an index.html file in that location so that we can test that your new server block works as expected:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

Now go to your browser and try to open your website URL using IP address:

http://Public-IP-Address:80 or http://Public-DNS-Name:80
  
  <img width="1440" alt="Screenshot 2022-10-03 at 10 58 05" src="https://user-images.githubusercontent.com/111234300/193596756-92a7e34f-e269-4e7c-8d60-71c8635849df.png">
<img width="1440" alt="Screenshot 2022-10-03 at 10 59 20" src="https://user-images.githubusercontent.com/111234300/193597011-c1080f32-f596-4f15-8ab8-dd3c7cf8c831.png">
  
  
  Our LEMP stack is now fully configured. In the next step, we’ll create a PHP script to test that Nginx is in fact able to handle .php files within your newly configured website.
  
  #### STEP 5
  
Type the code
  `sudo vi /var/www/projectLEMP/info.php`
  
  and input 
  `<?php
phpinfo();`

<img width="169" alt="Screenshot 2022-10-03 at 11 02 59" src="https://user-images.githubusercontent.com/111234300/193598330-2b6543e3-6619-4acc-b846-e7afa980328c.png">

Now, we can access it in our web server 
http://Public-IP-Address/info.php or http://Public-DNS-Name/info.php

<img width="1440" alt="Screenshot 2022-10-03 at 11 52 30" src="https://user-images.githubusercontent.com/111234300/193598660-1945affe-125d-45ea-8b7a-09fc26075a9a.png">
<img width="1440" alt="Screenshot 2022-10-03 at 11 52 36" src="https://user-images.githubusercontent.com/111234300/193598889-30098463-ad8a-43c0-995d-79c913c1f338.png">

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file:

`sudo rm /var/www/your_domain/info.php`

You can always regenerate this file if you need it later.

#### STEP 6
RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

THIS IS THE FINAL STEP TO THIS WEB DEPLOYMENT SOLUTION

First, connect to the MySQL console using the root account:
`sudo mysql`

To create a new database, run the following command from your MySQL console:
``mysql> CREATE DATABASE `example_database`;``

Now we will create a new user and grant him full privileges on the database you have just created.
PLEASE USE ANY PASSWORD OF YOUR CHOICE - I USED `password2`

`mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

Now we need to give this user permission over the example_database database:
`mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

<img width="774" alt="Screenshot 2022-10-03 at 11 57 58" src="https://user-images.githubusercontent.com/111234300/193605812-147c5eac-be08-4130-9017-4e629c04eeba.png">
<img width="636" alt="Screenshot 2022-10-03 at 11 59 27" src="https://user-images.githubusercontent.com/111234300/193605832-bfc4711e-8043-4d02-8edc-00dc6b64bba7.png">

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

`CREATE TABLE example_database.todo_list (
     item_id INT AUTO_INCREMENT,
     content VARCHAR(255),
     PRIMARY KEY(item_id)
);`

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:
`mysql> INSERT INTO example_database.todo_list (content) VALUES ("AWS CLOUD PRACTITIONER");`

To confirm that the data was successfully saved to your table, run:

`mysql>  SELECT * FROM example_database.todo_list;`

You’ll see the following output:

<img width="453" alt="Screenshot 2022-10-03 at 12 20 23" src="https://user-images.githubusercontent.com/111234300/193606468-86e3e238-540f-4c62-8815-55ccd72f4451.png">

Then, exit MYSQL

Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

vi /var/www/projectLEMP/todo_list.php

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, displays the results in a list. If there is a problem with the database connection, it will throw an exception.

Copy this content into your todo_list.php script:

<img width="705" alt="Screenshot 2022-10-03 at 12 46 01" src="https://user-images.githubusercontent.com/111234300/193606980-857d4ace-e428-47fc-8eaf-7f8123bc6195.png">

Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

http://Public-IP-Address/todo_list.php or http://Public-DNS-Name/todo_list.php

You should see a page like this, showing the content you’ve inserted in your test table:

<img width="1440" alt="Screenshot 2022-10-03 at 13 23 16" src="https://user-images.githubusercontent.com/111234300/193607496-b12e753d-291b-4815-a0ac-f9340b7683f4.png">
<img width="1440" alt="Screenshot 2022-10-03 at 13 24 52" src="https://user-images.githubusercontent.com/111234300/193607510-5d296422-151e-47da-a71e-1d630ae47899.png">

That brings us to the end of this deployment:
We have just built a flexible foundation for serving PHP websites and applications to our visitors, using Nginx as web server and MySQL as database management system.







