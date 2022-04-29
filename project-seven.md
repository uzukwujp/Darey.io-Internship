## DEVOPS TOOLING WEBSITE SOLUTION
This project builds on the 3-tier architecture we saw earlier by adding a Network file System(NFS) to our system architecture(website). The NFS host the static files needed to server our site to the public while the database stores the dynamic data. This architecture makes our webserver stateless: meaning we can easily configure and  scale our webserver up or down as need arises.

### Step 1 – Prepare NFS Server

- Spin up a new EC2 instance with RHEL Linux 8 Operating System.

- Based on your LVM experience from Project 6, Configure LVM on the Server.

- Instead of formating the disks as ext4 you will have to format them as xfs

- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

- Create mount points on /mnt directory for the logical volumes as follow:

  ```  
       Mount lv-apps on /mnt/apps – To be used by webservers
       Mount lv-logs on /mnt/logs – To be used by webserver logs
       Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8
  
  ```
  
- Install NFS server, configure it to start on reboot and make sure it is u and running

  ```  
  
      sudo yum -y update
      sudo yum install nfs-utils -y
      sudo systemctl start nfs-server.service
      sudo systemctl enable nfs-server.service
      sudo systemctl status nfs-server.service
  
  
  ```
  
- Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in         production set up you would probably want to separate each tier inside its own subnet for higher level of security.
  To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link: 
  
  
- Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

  ```
       sudo chown -R nobody: /mnt/apps
       sudo chown -R nobody: /mnt/logs
       sudo chown -R nobody: /mnt/opt

       sudo chmod -R 777 /mnt/apps
       sudo chmod -R 777 /mnt/logs
       sudo chmod -R 777 /mnt/opt

       sudo systemctl restart nfs-server.service
       
  ```
  
- Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):


  `sudo vi /etc/exports`


  ![Nfs-p7](https://user-images.githubusercontent.com/52359007/165958878-8017f075-6290-451f-ac36-5b2b87131b5a.PNG)
  
  
- Check which port is used by NFS and open it using Security Groups (add new Inbound Rule):

  `rpcinfo -p | grep nfs`
  
   
  ![pl7](https://user-images.githubusercontent.com/52359007/165959535-d33f8311-a195-4625-a116-24223ff2b8a4.PNG)
 
 
- Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049

  
  ![port7](https://user-images.githubusercontent.com/52359007/165959979-6c3702cc-209a-4559-a5b2-c890d6854c76.PNG)
  
  

### Step-2 Configure the Database Server

- Install MySQL server

- Create a database and name it tooling

- Create a database user and name it webaccess

- Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr



### Step 3 — Prepare the Web Servers

- Launch a new EC2 instance with RHEL 8 Operating System

- Install NFS client

  `sudo yum install nfs-utils nfs4-acl-tools -y`
  
- Mount /var/www/ and target the NFS server’s export for apps

  ```  
       sudo mkdir /var/www
       sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
  
  ```
  
- Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:

  `sudo vi /etc/fstab`
   
- add following line

  `<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0`
  
- Install Remi’s repository, Apache and PHP


  ```
       
       
       sudo yum install httpd -y

       sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

       sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

       sudo dnf module reset php

       sudo dnf module enable php:remi-7.4

       sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

       sudo systemctl start php-fpm

       sudo systemctl enable php-fpm

       setsebool -P httpd_execmem 1
  ```
  
- Repeat steps 1-5 for another 2 Web Servers.

- Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means     NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.


- Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after         reboot.

- Fork the tooling source code from Darey.io Github Account to your Github account. (Learn how to fork a repo here)

- Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html


- Open TCP port 80 to allow access from the browser

- Disable SElinux using this command: `sudo setenforce 0`. To make it parmanent use the command below and set *SELINUX=disabled*

  `sudo vi /etc/sysconfig/selinux`
  
- Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command   mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql

- Create in MySQL a new admin user with username: myuser and password: password:

  ```
      
      INSERT INTO ‘users’ (‘id’, ‘username’, ‘password’, ’email’, ‘user_type’, ‘status’) VALUES
      -> (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);
  ```
  
- Open the website in your browser
  
- You should something like the screenshots below:
  
  
  ![project 7b](https://user-images.githubusercontent.com/52359007/165965466-7310af56-b2d5-486a-b746-a9cbb6f30621.PNG)
  
  
  
  
   ![project 7](https://user-images.githubusercontent.com/52359007/165965578-0cde88ea-127c-4f41-8911-37873aeefee7.PNG)
