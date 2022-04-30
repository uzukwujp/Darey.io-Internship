## LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS
In this project we will be implementing load balancing using Nginx. Nginx is a powerful software able to handle many concurrent connections. We will also be implementing SSL/TLS. Which secures the site from Man in the Middle Attack.

### Configure Nginx as a Load Balancer

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is     used for secured HTTPS connections)

- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

- Update the instance and Install Nginx

  ``` sudo apt update
      sudo apt install nginx
  ```
  
- Open the default nginx configuration file and paste the text below:

  `sudo vi /etc/nginx/nginx.conf`


  ``` 
      insert following configuration into http section

      upstream myproject {
      server Web1 weight=5;
     server Web2 weight=5;
     }

     server {
        listen 80;
        server_name www.domain.com;
        location / {
        proxy_pass http://myproject;
    }
    }

    #comment out this line
    # include /etc/nginx/sites-enabled/*;
  ```
  
- Restart Nginx and make sure the service is up and running

  ```
      sudo systemctl restart nginx
      sudo systemctl status nginx
  
  ```
  
  
  ![project10- nginx](https://user-images.githubusercontent.com/52359007/166102946-bb6b24dc-ff12-4325-8475-4f09fc54e0ec.PNG)
  
  


### Register a new Domain Name and Configure Secured Connection using SSL/TLS Certificates

- Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

- Update A record in your registrar to point to Nginx LB using Elastic IP address

  
  ![project10-route53 record](https://user-images.githubusercontent.com/52359007/166103130-ef8481d1-c0fd-4484-b519-3e51de9c41d1.PNG)
  
  
- Configure Nginx to recognize your new domain name
  Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com
  
  
- Install certbot and request for an SSL/TLS certificate
  Make sure snapd service is active and running
  
  `sudo systemctl status snapd`
  
- Install certbot

  `sudo snap install --classic certbot`
  
  
- Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be   looked up from nginx.conf file so make sure you have updated it on step 4).



   
  ![project10-ssl enabled site](https://user-images.githubusercontent.com/52359007/166103265-385ed21f-616a-4869-bc0d-8882052c7700.PNG)  
 
 
- Set up periodical renewal of your SSL/TLS certificate
  By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.
  
  
- You can test renewal command in dry-run mode

  `sudo certbot renew --dry-run`
  
  
- Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

- To do so, lets edit the crontab file with the following command:

  `crontab -e`
  
- Add following line:


  `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`
