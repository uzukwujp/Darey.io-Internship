## SIMPLE TO-DO APPLICATION ON MERN WEB STACK

In this project, you are tasked to implement a web solution based on MERN stack in AWS Cloud.

- MERN Web stack consists of following components:

- MongoDB: A document-based, No-SQL database used to store application data in a form of documents.

- ExpressJS: A server side Web Application framework for Node.js.

- ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.

- Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

### Step 0 – Preparing prerequisites

You can follow same steps as step 0 in [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md)

### Step 1 – Backend configuration

- Update ubuntu:

  `sudo apt update`
  
- Upgrade ubuntu:

  `sudo apt upgrade`
  
- Lets get the location of Node.js software from Ubuntu repositories:

  `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
  
- Install Node.js with the command below:

  `sudo apt-get install -y nodejs`
  
- The command above installs both Node and NPM packages.

- To Verify the version of Nodejs and NPM installed, run the command below:

  `node -v; npm -v`
  
- You should see a screenshot like the one below on your terminal:

  ![pbl3](https://user-images.githubusercontent.com/52359007/165491807-f543be0f-847b-4fec-89df-03ee5bf909c3.PNG)
  
- Create a new directory for your To-Do project:

  `mkdir Todo`
  
- Changed into Todo directory with the command below:

  `cd Todo`
  
- Initialise a project by running the command below:

  `npm init`
  
- When prompted answer accordingly and you should see something like the screenshot below:

  


  
  
