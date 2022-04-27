## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
Mark as CompletedSubmit Project for Review
Now, when you have already learned how to deploy LAMP, LEMP and MERN Web stacks – it is time to get yourself familiar with MEAN stack and deploy it to Ubuntu server.

MEAN Stack is a combination of following components:
- MongoDB (Document database) – Stores and allows to retrieve data.
- Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
- Angular (Front-end application framework) – Handles Client and Server Requests
- Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

### Step 0 – Preparing prerequisites
Refer to step 0 of [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md). Its same step.

### Step 1: Install NodeJs
- Update ubuntu by running the command below:

  `sudo apt update`
  
- Upgrade ubuntu by running the command below:

  `sudo apt upgrade`
  
- Add certificates:

  ```
      sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

      curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
  ```
- Install NodeJS:

  `sudo apt install -y nodejs`
  

### Step 2: Install MongoDB
Mongodb is a Document based database suitable for semi-structured data.

- Run the commands below to seed the database add momgodb repository to your Ubuntu server:

  
      `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`
  
  
      `echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`
  

- Install MongoDB:

  `sudo apt install -y mongodb`
  
- Start The server:

  `sudo service mongodb start`
  
- Verify that the service is up and running

  `sudo systemctl status mongodb`
  
- You terminal should look like the screenshot below:

  
