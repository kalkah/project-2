# Documentation for AWS LEMP STACK IMPLEMENTATION

# WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS

### This project shows how to implement LAMP Stack on AWS. LAMP stack comprises of four technologies - Linux, Nginx, Mysql and PhP

After launching the Ubuntu virtual server on AWS, the server was updated using the command below

**`Sudo apt update`**

### The Nginx webserver was installed using the command below

**`Sudo apt install nginx`**

The status of Nginx webserver is confirmed using the command below

**`Sudo systemctl status nginx`**

The image show the status of the nginx webserver

<img width="731" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/d2a634c1-2487-4898-ad64-9c9b6f98145a">

The command below was used to confirm if the server is accessible locally

**`curl http://localhost:80`**  using the DNS name  

<img width="556" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/49d57c47-8028-40c1-9f93-a3e25b11b106">


**`curl http://127.0.0.1:80`** command also does the same thing as the command above  using the IP address that corresponds to the DNS name 'localhost

<img width="447" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/80cc0cfe-a0c1-49e9-aeda-a87ea4c1666f">

I also conirmed that the Nginx webserver can be access from the internet using the EC2 public address.

**`http://18.232.74.244`**

<img width="685" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/2e60c33e-0d57-44dc-af73-f09a7553bb95">

### Mysql was used as the database management system, mysql was installed using the command below

**`sudo apt install mysql-server`**

The following command was used to log in to the MySQL console

**`sudo mysql`**

<img width="467" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/a9e781c3-020d-4049-be62-485fe557d847">

A root user password was set using mysql_native_password as default authentication method.

**`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'Password.1';`**

I exit the MySQL console to start MYSQL interactive script using the command below. This remove some insecure default settings and lock down access to the database system. This script also prompt me to configure the validate password plugin.

**`sudo mysql_secure_installation`**

<img width="392" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/27caed7b-848b-497d-bf17-3b419d5d3b03">

The command below was used to test login access to the MYSQL console

**`sudo mysql -p`** -p flag prompt me for password.

<img width="450" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/e8e3bb40-0d43-468f-be08-d4da495c9eb2">

### The fourth technology in the stack is PHP. PHP process code to display dynamic content to the end user. 

Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and webserver. PHP fastCGI process manager (fpm) will be install. it will tell Nginx to pass PHP requests to  this software for processing.  php-mysql was also installed, it is a php module that allow php to communicate with MySQL-based databases.

**`sudo apt install php-fpm php-mysql`** 

This command **`php -v`** was run to confirm the version of my PHP

<img width="440" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/e2a77b80-5e06-461d-81a7-3707ed7fcf76">

The LEMP stack installation is completed. The next step is configuring Nginx to use PHP Processor

### Configuring Nginx to use PHP Processor

On Ubuntu 20.04 Nginx has one server block enabled by default and is cobfigured to serve document out of a directory  at /var/www/html. In order to be able to host more than one domain in a single server, server blocks was created to encapsulate configuration details. projectLEMP was used as teh domain name.

Root web directory was created for projectLEMP

**`sudo mkdir /var/www/projectLEMP`**

The ownership of the directory was assigned with $USER enviroment variable

**`sudo chown -R $USER:$USER /var/www/projectLEMP`**

A new configuration file was created and opened in Nginx sites-available directory

**`sudo nano /etc/nginx/sites-available/projectLEMP`**

The created file is blank and the following command is pasted in the file.
```
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
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

The configuration is activated by linking to the config file from Nginx's sites-enabled directory using the code below

**`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`**

The command above will tell nginx to use the configuration next it reloaded. Syntax error in the configuration file was tested using the command below

**`sudo nginx -t`**

<img width="440" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/04ba2e31-67bc-4ddf-b522-65c975f58b7b">

The default Nginx host that is currently configured to listen on port 80 was diabled with the command below for our new configuration to run.

**`sudo unlink /etc/nginx/sites-enabled/default`**

Nginx was reloaded to apply the changes

**`sudo systemctl reload nginx`**

The website is active but our web root directory (/var/www/projectLEMP) is still empty. An index was created in the loacation with the command below

**`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`**

The website was accessed on the browser using the public IP Address: **`http://18.232.74.244:80`**. DNS name was also be use to access the website.

<img width="632" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/f7f40486-6594-4d12-9cbe-8ec351a14bbf">

### Testing PHP with Nginx

The LEMP stack is completely installed and fully operational. I want to test that Nginx can correctly handover .php files to PHP processor. To do this a PHP test file will be created in the document.

**`nano /var/www/projectLEMP/info.php`**

The follwing line of code was pasted in the file created above

```
<?php
phpinfo();
```
The PHP page can be accessed from the browser using the domanin name or the public IP addressin the Nginx configuration file followed by `/info.php

**`http://18.232.74.244/info.php`**

The image is shown below

<img width="773" alt="image" src="https://github.com/kalkah/project-2/assets/95209274/9deed09b-eaad-4192-8754-f6f816f66e3e">
