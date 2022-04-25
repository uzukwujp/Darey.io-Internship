# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
### What is a Technology stack?
A technology stack is a set of tools or frameworks that is known to work nicely together to produce properly functional software products. Examples are:
- LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
- LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
- MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
- MEAN (MongoDB, ExpressJS, AngularJS, NodeJS
#### Step 0 – Preparing prerequisites
- [AWS account setup and Provisioning of an Ubuntu server](https://www.youtube.com/watch?v=xxKuB9kJoYM&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=7)
- [Connecting to EC2 instance](https://www.youtube.com/watch?v=TxT6PNJts-s&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=8)
#### STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL
Install Apache using Ubuntu’s package manager ‘apt’:

- #update a list of packages in package manager

`sudo apt update`

- #run apache2 package installation

`sudo apt install apache2`

- To verify that apache2 is running as a Service in our OS, use following command

`sudo systemctl status apache2`

- You should find a printout on your console like the one below:

![apache2](https://user-images.githubusercontent.com/52359007/165087732-758d73f5-ec5d-44a2-9552-7d6097176d64.PNG)

- To publicly access your webserver on your browser add new security rule to open TCP port 80 like so:

![TCP 80](https://user-images.githubusercontent.com/52359007/165089230-c566c252-fe9e-4d7b-b915-89820d04386f.PNG)

- Access your webserver using the url below:

`http://<Public-IP-Address>:80`

- You find a picture like this below:

![Screenshot (3)](https://user-images.githubusercontent.com/52359007/165090907-0875b135-ede3-4e79-b6b2-b3897f4f8f92.png)

#### STEP 2 — INSTALLING MYSQL

- Install MYSQL

`sudo apt install mysql-server`

- Run preinstalled mysql script to lock down database access. It will prompt to **Configure Password validation**. Enter Y to accept or any other character to object. Enter Y subsequently when prompted to complete the process.

`sudo mysql_secure_installation`

- Confirm mysql is running by running the command below:

`sudo systemctl status mysql`

You should find screenshot below on your console:

![mysql-running](https://user-images.githubusercontent.com/52359007/165094985-a6c09cf9-0c29-49f1-91b2-53690210628c.PNG)

- Confirm you can connect to mysql by running the command below:

`sudo mysql`

you should find the picture below on your console:

![mysql](https://user-images.githubusercontent.com/52359007/165095650-d8df8314-7541-4418-977d-1a563ca30d28.PNG)

- Exit from mysql by running the command below:

`exit`

#### STEP 3 — INSTALLING PHP
PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files.

- To install these 3 packages at once, run:

`sudo apt install php libapache2-mod-php php-mysql`

- confirm the version of PHP by running the command below:

`php -v`

- You should find something like this below:

![Php](https://user-images.githubusercontent.com/52359007/165097415-9b927978-be3b-4087-9e58-53f0eba8fe23.PNG)


#### STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE
Apache webserver serves content placed in its document root located at path: `/var/www/html`. This is fine when Apache is required to serve one website. However when Apache is required to serve multiple websites, a **Virtual Host** is configured. **Virtual Host** servers as a container for each website. You require one for each website to be served.

- Create a directory for projectlamp in */var/www/* directory by running the command below:

  `sudo mkdir /var/www/projectlamp`

- Set ownership of projectlamp directory to the logged-in user using the command below:

  `sudo chown -R $USER:$USER /var/www/projectlamp`

- Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor.

  `sudo vi /etc/apache2/sites-available/projectlamp.conf`
  
- This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

  `<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

- To save and close the file, simply follow the steps below:
  - Hit the esc button on the keyboard
  - Type :
  - Type wq. w for write and q for quit
  - Hit ENTER to save the file

- You can use the ls command to show the new file in the sites-available directory

  `sudo ls /etc/apache2/sites-available`
  
- You will see something like this;

  `000-default.conf  default-ssl.conf  projectlamp.conf`

- With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

- You can now use a2ensite command to enable the new virtual host:

`sudo a2ensite projectlamp`

- You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:

`sudo a2dissite 000-default`

- To make sure your configuration file doesn’t contain syntax errors, run:

`sudo apache2ctl configtest`

- Finally, reload Apache so these changes take effect:

`sudo systemctl reload apache2`

- Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`

- Now go to your browser and try to open your website URL using IP address:

`http://<Public-IP-Address>:80`

- You should find something like this below:






















