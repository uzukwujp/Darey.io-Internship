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

#update a list of packages in package manager

`sudo apt update`

#run apache2 package installation

`sudo apt install apache2`

To verify that apache2 is running as a Service in our OS, use following command

`sudo systemctl status apache2`

You should find a printout on your console like the one below:






