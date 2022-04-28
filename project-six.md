## WEB SOLUTION WITH WORDPRESS
In this project, we implement the 3 -tier architecture. In this architecure, three servers host the database, business logic, and the presentation layer of the application. We also practices the creation of disk partitions, and logical volume in this project.

### Step 0 - provisioning of Ec2 instances
Again refer to step 0 of [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md) 


### Step 1 — Prepare a Web Server

- Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

- Open up the Linux terminal to begin configuration

- Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory.   Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

- In the screenshot below, you should the 3 disk: *xvdf*, *xvdh*, *xvdg*

  
  ![pbl6](https://user-images.githubusercontent.com/52359007/165776098-bf177f9a-a553-422d-aac7-0911e021c9c7.PNG)
  
  
- Use gdisk utility to make partitions on each of the disk using the GPT partition scheme

  `sudo gdisk /dev/xvdf`
  
  
- Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

  ```
     sudo pvcreate /dev/xvdf1
     sudo pvcreate /dev/xvdg1
     sudo pvcreate /dev/xvdh1
  ```
  
- Verify that your Physical volume has been created successfully by running *sudo pvs*

- You find on your terminal something similar to the screenshot below:


  ![pvs](https://user-images.githubusercontent.com/52359007/165777821-22bd99a2-40c7-44cc-ab93-02b7865aaea7.PNG)
  
- Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

  `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
  
- Verify that your VG has been created successfully by running *sudo vgs*


  ![vgs](https://user-images.githubusercontent.com/52359007/165778608-4b64e027-82c3-40b2-9be1-40bfda66c773.PNG)
  

- Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used     to store data for the Website while, logs-lv will be used to store data for logs. 


  ```
      sudo lvcreate -n apps-lv -L 14G webdata-vg
      sudo lvcreate -n logs-lv -L 14G webdata-vg
  ```

- Verify that your Logical Volume has been created successfully by running *sudo lvs*

  
  ![lvs](https://user-images.githubusercontent.com/52359007/165779464-0c0a2f34-b578-4f00-a0e5-b1149ae5b63d.PNG)
  
  
- Use mkfs.ext4 to format the logical volumes with ext4 filesystem

  ```
      sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
      sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
  ```
  
- Create /var/www/html directory to store website files

  `sudo mkdir -p /var/www/html`
  
- Create /home/recovery/logs to store backup of log data

  `sudo mkdir -p /home/recovery/logs`
  
- Mount /var/www/html on apps-lv logical volume

  `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
  
  
- Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

  `sudo rsync -av /var/log/. /home/recovery/logs/`
  
- Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
  important)
  
  `sudo mount /dev/webdata-vg/logs-lv /var/log`
  
- Restore log files back into /var/log directory

  `sudo rsync -av /home/recovery/logs/. /var/log`
  
- Update /etc/fstab file so that the mount configuration will persist after restart of the server.

- The UUID of the device will be used to update the /etc/fstab file;

- You should find something like the screenshot below:


  ![blk](https://user-images.githubusercontent.com/52359007/165782553-c4c4ad99-cfe5-45a7-b181-50a4b2606bf2.PNG)
  
- Update /etc/fstab using the UUID generated for each partion respectively see example below:

  
  ![fstab](https://user-images.githubusercontent.com/52359007/165783479-9a8a45d1-b2bb-4b37-8adb-65177ab31a28.PNG)  
  
- Test the configuration and reload the daemon

  ```
      sudo mount -a
      sudo systemctl daemon-reload
  ```
  
- Verify your setup by running df -h, output must look like this:

  
  ![dh](https://user-images.githubusercontent.com/52359007/165784230-7e91d036-8082-49db-a21f-5226d783476d.PNG) 



### Step 2 — Prepare the Database Server
Repeat the disk partition process on DB server. However mount db-lv on */db/* directory instead


### Step 3 — Install WordPress on your Web Server EC2

- Update the repository

  `sudo yum -y update`
  
- Install wget, Apache and it’s dependencies

  `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json`
  
- Start Apache

  ``` 
      sudo systemctl enable httpd
      sudo systemctl start httpd
  ```

- To install PHP and it’s depemdencies

  ```   
       sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
       sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
       sudo yum module list php
       sudo yum module reset php
       sudo yum module enable php:remi-7.4
       sudo yum install php php-opcache php-gd php-curl php-mysqlnd
       sudo systemctl start php-fpm
       sudo systemctl enable php-fpm
       setsebool -P httpd_execmem 1    
  
  ```
  
- Restart Apache

  `sudo systemctl restart httpd`
  
  
- Download wordpress and copy wordpress to var/www/html


  ``` mkdir wordpress
      cd   wordpress
      sudo wget http://wordpress.org/latest.tar.gz
      sudo tar xzvf latest.tar.gz
      sudo rm -rf latest.tar.gz
      cp wordpress/wp-config-sample.php wordpress/wp-config.php
      cp -R wordpress /var/www/html/
  
  ```
  
  
- Configure SELinux Policies

  ```
       sudo chown -R apache:apache /var/www/html/wordpress
       sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
       sudo setsebool -P httpd_can_network_connect=1
  ```
  
  
### Step 4 — Install MySQL on your DB Server EC2


 ``` 
     sudo yum update
     sudo yum install mysql-server
 
 ```
 
 
- Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even     after reboot:

  ```
  
        sudo systemctl restart mysqld
        sudo systemctl enable mysqld
  
  ```
 
 
### Step 5 — Configure DB to work with WordPress

 ```
 
     sudo mysql
     CREATE DATABASE wordpress;
     CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
     GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
     FLUSH PRIVILEGES;
     SHOW DATABASES;
     exit
 
 ```
 
### Step 6 — Configure WordPress to connect to remote database.

- Open TCP port 3306 on DB server to allow connection to the database. Also set bind-address to 0.0.0.0

- Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

  ```sudo yum install mysql
     sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
  ```
  
- Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.

- You should find something like the screenshot below:
 

  ![project 6-database](https://user-images.githubusercontent.com/52359007/165789220-6d108077-fc0f-4299-9726-92573b91871a.PNG)
  
- Open TCP port 80 and access the website on your using your public IP:

  
- You should see something like the screenshot below on your terminal:

  
  ![wordpress2-project 6](https://user-images.githubusercontent.com/52359007/165789830-4d719d77-b535-428f-9079-290aa24c4b08.PNG)
  
  
  

   ![wordpress-project 6](https://user-images.githubusercontent.com/52359007/165789935-cc77a4b6-6e2a-43cd-9127-486a27e153de.PNG)




  
  
  


