## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS
In [project-eight](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-eight.md) we practices horizontal scaling. We deployed a load balancer which distibutes traffic between two webservers. Manually configuring two webservers is not labourous but how about configuring thousands of servers. There where automation comes in.

In this project we introduce Jenkins. Jenkins is an open source self contained automation server. We will be using Jenkins free style project to automate pulling changes in our github repository, build, test and update our NFS server.


### Step 1 – Install Jenkins server

- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

- Install JDK (since Jenkins is a Java-based application)

  ```    
       sudo apt update
       sudo apt install default-jdk-headless
  ```
  
- Install Jenkins


  ```
       wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
       sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
       /etc/apt/sources.list.d/jenkins.list'
       sudo apt update
       sudo apt-get install jenkins
  ```
  
- Make sure Jenkins is up and running


  `sudo systemctl status jenkins`
  
- By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

- Perform initial Jenkins setup

  
  ![jenkings-landing](https://user-images.githubusercontent.com/52359007/166102045-ea2dace0-29d2-4823-8761-4a6e2e51ae57.PNG)
  


### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

- Enable webhooks in your GitHub repository settings

  
  ![jenkins-webhook](https://user-images.githubusercontent.com/52359007/166102102-6baa7568-946e-4368-a364-0cf6ca6aa904.PNG)
  
  
- Go to Jenkins web console, click "New Item" and create a "Freestyle project"
