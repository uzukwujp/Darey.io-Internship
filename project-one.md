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












