## WEB STACK IMPLEMENTATION (LEMP STACK)
In this project you will implement a similar stack as in [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md), but with an alternative Web Server – NGINX, which is also very popular and widely used by many websites in the Internet

### Step 0 – Preparing prerequisites
- You need an AWS account and Ubuntu server provisioned. You can follow step 0 of [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md) to achieve that.

- However instead of connecting to the EC2 instance with **Putty** you can use Git Bash. Download and install git bash from this video: [GitBash](https://www.youtube.com/watch?v=qdwWe9COT9k)

- Launch Git Bash and run following command:

  `ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address>`
  
- You should see something like the screenshot below on your console:

  ![image](https://user-images.githubusercontent.com/52359007/165129115-b9b7fd15-fbf2-45c0-86f5-18bebfc74893.png)
  
 
 
 
### Step 1 – Installing the Nginx Web Server
In [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md) we used Apache as our webserver. An alternative is Nginx. Nginx is known for its ability to handle multiple open connections effectively. 

- To Update your Package Repository run the command below:

  `sudo apt update`
  
- To install Nginx run the command below:

  `sudo apt install nginx`

- To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

  `sudo systemctl status nginx`
  
- You should the something like the screenshot below:

  ![Nginx-running](https://user-images.githubusercontent.com/52359007/165132482-f39f1c3f-d2d5-425b-bd26-6efe52c15639.PNG)
  
- Confirm that Nginx can handle request from the internet by lunching it on the browser using the *URL* below:

  `http://<Public-IP-Address>:80`
  
- You can access your Public-IP-Address from your EC2 instance.

- You should find something like the screenshot below if nginx is running:

  ![nginx](https://user-images.githubusercontent.com/52359007/165133741-133f1b10-39d4-4c84-8107-966cdd347c13.PNG)
  
  
### Step 2 — Installing MySQL

- Run the command below to install mysql:

  `sudo apt install mysql-server`
  
 When prompted, confirm installation by typing Y, and then ENTER.
 
- To secure your database run the preinstalled script below:

  `sudo mysql_secure_installation`
  
 This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Answer Y for yes or any character for No. Answer Y subsequently when prompted to complete the       process.
 
- Run the command below to confirm mysql is running:

  `sudo systemctl status mysql`
  
- You should find something like the screenshot below:

  ![pbl-mysql](https://user-images.githubusercontent.com/52359007/165136393-fd765f91-e080-4c76-bad1-2759ee094f16.PNG)
  
- Confirm that you have access to the database by running the command below:

  `sudo mysql`
  
- Exit the database with the command below:

  `exit`
  
  
### Step 3 – Installing PHP 
While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

- To install these 2 packages at once, run:

  `sudo apt install php-fpm php-mysql`
  
  When prompted, type Y and press ENTER to confirm installation.


### Step 4 — Configuring Nginx to Use PHP Processor
When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

- Create the root web directory for your_domain as follows:

  `sudo mkdir /var/www/projectLEMP`
  
- Set logged-in user as the owner of the directory  */var/www/projectLEMP/*

  `sudo chown -R $USER:$USER /var/www/projectLEMP`
  
- open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor.

  `sudo nano /etc/nginx/sites-available/projectLEMP`
  
- This will create a new blank file. Paste in the following bare-bones configuration:

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

- listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.
root — Defines the document root where the files served by this website are stored.
index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.
server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.
- location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.
- location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.
- location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.


- When you’re done editing, save and close the file. If you’re using nano, you can do so by typing CTRL+X and then y and ENTER to confirm.

- Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

  `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
  
- You can test your configuration for syntax errors by typing:

  `sudo nginx -t`
  
- You shall see following message:

 `nginx: the configuration file /etc/nginx/nginx.conf syntax is ok`
 
 `nginx: configuration file /etc/nginx/nginx.conf test is successful`
 
- Disable default Nginx host that is currently configured to listen on port 80, for this run:

  `sudo unlink /etc/nginx/sites-enabled/default`
  
- Reload Nginx to apply the changes:

  `sudo systemctl reload nginx`
  
- Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new     server block works as expected:

  sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s
  http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
 
- Now go to your browser and try to open your website URL using IP address:

  `http://<Public-IP-Address>:80`
  
  
### Step 5 – Testing PHP with Nginx

Your LEMP stack should now be completely set up.

At this point, your LAMP stack is completely installed and fully operational.

You can test it to validate that Nginx can correctly hand .php files off to your PHP processor.

You can do this by creating a test PHP file in your document root.

- Open a new file called info.php within your document root in your text editor:

  `sudo vi /var/www/projectLEMP/info.php`
  
- paste the following lines below into the new file.

  <?php
  phpinfo();

- You can access your website with the Url below:

  `http://`server_domain_or_IP`/info.php`

- You should see something like the screenshot below:

  ![Screenshot (6)](https://user-images.githubusercontent.com/52359007/165144750-81b8ed9c-edbd-49eb-9577-35fda4928863.png)

- The screenshot above contains sensitive information of your environment. So it is standard practice to remove it. Run the command below:

  `sudo rm /var/www/your_domain/info.php`


### Step 6 — Retrieving data from MySQL database with PHP


  




  
 
 
 



  
  

  
  
   

