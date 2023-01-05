**DEPLOYMENT OF A REAL LIFE APPLICATION OF LARAVEL USING DEBIAN 11/BULLSEYE64 ON DIGITALOCEAN**

To start this project which involves deploying a Laravel app using Apache, installing Php and all its' dependencies and also creating a database using Msql, i first had to install LAMP Stack(Linux, Apache, MySQL, PHP) which is an acronym denoting one of the most common software stacks for many of the web's most popular applications.

**Prerequisites to Install LAMP**
<ul>
<li>Root access to server or a sudo user.</li>

<li>Domain pointed to my server IP to install Let’sEncrypt SSL.</li>
</ul>

**Step 1: SETUP INITIALIZATION**

 Started by updating and upgrading packages using the commands:

` apt upadate`

 `apt upgrade`

 `apt install curl`

respectively since i was working as a root user. Then checked to confirm that wget has been  pre-installed using:

`wget --version`

which it was.

**Step 2: SETUP HOSTNAME AND TIMEZONE**

To setup my hostname with my domainname **bennieo.me**, did this using the command:

`hostnamectl set-hostname server.domainname`

Then to confirm, used command:

`hostnamectl`

The output is as shown below:

![image](https://user-images.githubusercontent.com/105982108/198457891-5ffc53d3-1dae-41a1-83e8-252dc8e4d89f.png)


Then to set timezone, used the command:

`timedatectl set-timezone Africa/Lagos`

**Step 3: EDIT HOSTS FILE**

Edited /etc/hosts file to include IP address of server and my domainname using the command:

`nano /etc/hosts`

The rendered output is as shown below:

![image](https://user-images.githubusercontent.com/105982108/198457360-52e4a189-c5a7-482c-92c2-cdab0bcdc2e7.png)


**Step 4: SETUP FIREWALL**

I set up Uncomplicated Firewall (UFW) with Apache to allow public access on default web ports for HTTP and HTTPS using command:

`apt install ufw`

Then to see all the listed applications, used the command:

`ufw app list`

Then installed the SSH, OpenSSH and WWW Full using:

`ufw allow SSH`

`ufw alllow OpenSSH`

`ufw allow 'WWW Full`

Then enabled them all using:

`ufw enable`

**Step 5: INSTALL APACHE 2**

Did this using:

`apt install apache2`

**Step 6: INSTALL MYSQL**

To do this i first installed latest releases package using:

`wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb` 

`sudo apt install ./mysql-apt-config_0.8.22-1_all.deb`

 Then installed using:

 `apt update`

`sudo apt install mysql-server`

To verify that Msql server is running i used:

`service mysql status`

The output is as shown below that it enabled and running:

![image](https://user-images.githubusercontent.com/105982108/198456566-2e03e89f-6634-4196-8a23-5b5443a868e6.png)



**SECURE MYSQL**

Used the follwing script to improve MySQL server security:

`sudo mysql_secure_installation`

**Step 7: INSTALL PHP**

Started by adding the Sury PPA for PHP 8.1

`sudo apt -y install lsb-release apt-transport-https ca-certificates`

`sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg`


Then added PPA to the server packages:

`echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list`

Now updated the packages and installed PHP 8.1 using:

` apt update`

`apt upgrade`

`sudo apt install php libapache2-mod-php php8.1-mysql php8.1-common php8.1-mysql php8.1-xml php8.1-xmlrpc php8.1-curl php8.1-gd php8.1-imagick php8.1-cli php8.1-dev php8.1-imap php8.1-mbstring php8.1-opcache php8.1-soap php8.1-zip php8.1-intl -y`

Then checked the version installed using:

`php -v`

![image](https://user-images.githubusercontent.com/105982108/198455939-ff8b79c9-12f3-4b83-8e8a-f915d6449e8a.png)


**Step 8: CONFIGURING PHP**

Now configured PHP for Web Applications by changing some values in **php.ini** file.
For PHP 8.1 with Apache the **php.ini** location will be in following directory.

`sudo nano /etc/php/8.1/apache2/php.ini`

I updated the following values for better performance

upload_max_filesize = 64M

post_max_size = 128M 

memory_limit = 256M 

max_execution_time = 300 

max_input_vars = 5000 

max_input_time = 150

![image](https://user-images.githubusercontent.com/105982108/198454461-b9f21554-6141-4753-bb85-a285c4426254.png)


![image](https://user-images.githubusercontent.com/105982108/198454888-2f949090-75a7-4b98-80e8-ef25d941c874.png)


![image](https://user-images.githubusercontent.com/105982108/198455322-e72a2959-65fd-4319-83f4-94eead0b6f1b.png)


Then restarted Apache for the changes to take effect

`systemctl restart apache2`

**Step 9:INSTALL COMPOSER**

I installed Composer beacause it is a tool for dependency management in PHP. It allows one to declare the libraries your project depends on and it will manage (install/update) them for you . Laravel uses Composer as a dependency manager, which keeps track of all the data necessary to handle packages.

 Installed the following:

`php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"`

`php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"`

`php composer-setup.php`

`php -r "unlink('composer-setup.php');"`

After it has been installed, moved composer.phar file to /usr/local/bin/composer using:

`sudo mv composer.phar /usr/local/bin/composer`

Then i assigned execute permission using:

`chmod +x   /usr/local/bin/composer`



**Step 10: CONFIGURING APACHE**

Started by disabling default Apache configuration which removes the default page using:

`sudo a2dissite 000-default`

Then changed directory into Apache website default directory

`cd /var/www`

**Cloning Git Repository**

Started by first installing git 

`apt install git`

Then cloned the laravel project from github using:

`git clone https://github.com/f1amy/laravel-realworld-example-app.git`

Then moved it into my document file using:

`mv laravel-realworld-example-app.gt bennieo`

Then to confirm this has been done changed directory into **bennieo** and checked the file and cd back to previous directory using:

`ls -al`

**Setup Permissions**

This was done using:

`chown -R www-data:www-data /var/www/html/domainname`

`chmod -R 755 /var/www/html/domainname`

Then i changed directory to **bennieo** and changed permissions using:

`sudo chgrp -R www-data storage bootstrap/cache`

`sudo chmod -R ug+rwx storage bootstrap/cache`

**Create a new virtual host configuration**

Did this using:

`sudo nano /etc/apache2/sites-available/domainname.conf`

![image](https://user-images.githubusercontent.com/105982108/198452879-861cc566-2bf8-46f6-ab34-5844e6925c1f.png)


Then enabled the new configuration using:

`sudo a2ensite domainname.conf`

**Create a .htaccess file**

The .htaccess file modifies website details without changing the configuration files. 

`nano .htaccess`

![image](https://user-images.githubusercontent.com/105982108/198452311-f5bc8ac9-9fc0-4433-a2f5-14595023d42f.png)


Then the mod_rewrite Apache module which helps manipulate URL requests on the server's side. Rules and patterns check an inputted URL through regular expressions and variables to extract information and serve the desired page. This is then used as follows:

`a2enmod rewrite`

After which the apache server is restarted but before this is done, first copied and then edited the .env file using:

`cp .env.example .env`

`nano .env`

![image](https://user-images.githubusercontent.com/105982108/198451687-e9182f0a-6c47-4a2d-999b-f227c9719eca.png)


**Step 11 CREATING DATABASE ON MYSQL**

Started by logging in using:

`sudo  mysql -u root -p`

To create a database, database user, and grant all privileges to the database user i ran the following commands:

`MariaDB [(none)]> CREATE DATABASE laravel_db;`

`MariaDB [(none)]> CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'Password';`

`MariaDB [(none)]> GRANT ALL ON laravel_db.* TO 'laravel_user'@'localhost';`

`MariaDB [(none)]> FLUSH PRIVILEGES;`

`MariaDB [(none)]> EXIT`

After creating it, to confirm it is working i exited and logged in with my credentials and it worked.

![image](https://user-images.githubusercontent.com/105982108/198451079-f7cdd466-3450-4adf-ad79-aad81c115304.png)


Then restarted apache using:

`systemctl restart apache2`

Then edited the web.php file by changing directory to routes where it is located.

![image](https://user-images.githubusercontent.com/105982108/198450552-3397e4cc-3606-45c4-89a9-f6fe9ec7f404.png)


Then i migrated database first by installing composer using:

`composer install`

Then checked for updates using:

`composer update`

Next is :

`composer create-project`

Then:

`php artisan migrate -- seed`

Then to check that there is no error in my config file before loading the app using:

`sudo apachectl configtest`

 `Syntax Ok`

Which gave Syntax Ok confirming everything was in place. Then checked the status of applications before reloading Apache using:

`systemctl status apache2`

`systemctl status mysql`

Which showed they were both running ok. Then now reloaded using:

`systemctl reload apache2`

Then inputted my domainname on my browser which showed laravel has been deployed without it being secure.

![image](https://user-images.githubusercontent.com/105982108/198449873-0efb74a5-d740-42ec-9598-9b1614db87e7.png)


**Step 12 INSTALLING LET'S ENCRYPT SSL**

HTTPS is a protocol for secure communication between a server (instance) and a client (web browser). This is a neccessity due to cyber crimes like hacking.
Started with installing Cerbot which will install Lets Encrypt SSL using snap.

`sudo apt update`

`sudo apt install snapd -y`

`sudo snap install core`

`sudo snap refresh core`

**Install Cerbot Tool**

`sudo snap install --classic certbot`

Next i configured Cerbot to be executable as a command using:

`sudo ln -s /snap/bin/certbot /usr/bin/certbot`

Then to execute the following command to install your certificates used:

`sudo certbot --apache --agree-tos --redirect -m youremail@email.com -d domainname.com -d www.domainname.com`

The output which shows it has been secured with the padlock unlike the previous image is shown below:

![image](https://user-images.githubusercontent.com/105982108/198448857-c80f91b7-7bd9-4e42-9bb8-040a400ea036.png)
