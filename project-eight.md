## LOAD BALANCER SOLUTION WITH APACHE

In project seven, We extended our 3- tier architecture for website by adding an NFS server. NFS holds static files shared across the webserver. This architecture makes our webservers stateless. However In our previous architecture, it implies we wll need to access each webserver directly from its public IP. This is not a good use of our resources because one server may unintentionally process all the request. 

To solve this problem, we need a single point of entry which routes traffic accross our webserver(3). Depending our configuration, the webserver can equally share the incoming request.

**Load Balancers** provide this single point of entry. They can software based or hardware based. Examples of software based load balancer are:

- Nginx

- Apache


### Step-0 Prerequisites
- Two RHEL8 Web Servers

- One MySQL DB Server (based on Ubuntu 20.04)

- One RHEL8 NFS server


### Configure Apache As A Load Balancer

- Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb,

- Open TCP port 80 on Project-8-apache-lb

- Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:


  ```
    
       #Install apache2
       sudo apt update
       sudo apt install apache2 -y
       sudo apt-get install libxml2-dev

       #Enable following modules:
       sudo a2enmod rewrite
       sudo a2enmod proxy
       sudo a2enmod proxy_balancer
       sudo a2enmod proxy_http
       sudo a2enmod headers
       sudo a2enmod lbmethod_bytraffic
  ```
  
- Restart apache2 service

  `sudo systemctl restart apache2`
  
- Make sure apache2 is up and running


  `sudo systemctl status apache2`
  
- Configure load balancing:


  ```
  
  
      sudo vi /etc/apache2/sites-available/000-default.conf

      #Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

      <Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
       </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

       #Restart apache server

       sudo systemctl restart apache2
  ```
  
- bytraffic balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic     must be distributed by loadfactor parameter.

  You can also study and try other methods, like: bybusyness, byrequests, heartbeat
  
  
- Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:


  `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`
  
- If in the Project-7 you mounted /var/log/httpd/ from your Web Servers to the NFS server – unmount them and make sure that each Web Server has its own log directory.


- Open two ssh/Putty consoles for both Web Servers and run following command:

  `sudo tail -f /var/log/httpd/access_log`

- You should see something like the screenshot below:

  
 ![pbl8](https://user-images.githubusercontent.com/52359007/165973367-d4164c5c-76f5-49cd-a7c3-853c82eea2b3.PNG)
 
 
- Access your webservers from the public IP of your load balancer:


  
  
  
  
 ![Screenshot (11)](https://user-images.githubusercontent.com/52359007/165973764-82ab202e-bb66-4094-83a9-b8bef5a328f8.png)  
