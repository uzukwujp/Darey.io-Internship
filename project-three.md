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

  ![json-pl3](https://user-images.githubusercontent.com/52359007/165494722-227a12c4-5653-40f6-8139-a785d7335860.PNG)
  
#### Install ExpressJS
- Install Express using npm:

  `npm install express`
  
- create a file index.js with the command below:

  `touch index.js`
  
- Install the dotenv module:

  `npm install dotenv`
  
- Open the index.js file with the command below:

  `vi index.js`
  
- In the blank open file, paste and save the text below:


```
  const express = require('express');
  require('dotenv').config();

  const app = express();

  const port = process.env.PORT || 5000;

  app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "\*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
  });

  app.use((req, res, next) => {
  res.send('Welcome to Express');
  });

  app.listen(port, () => {
  console.log(`Server running on port ${port}`)
  });
```

- Now run the command below: 

  `node index.js`
  
- You should find something like the screenshot below:

  ![pl3-node](https://user-images.githubusercontent.com/52359007/165498710-4e789811-294f-4ba7-9c7e-de1a252ab742.PNG)
  
#### Routes

There are three actions that our To-Do application needs to be able to do:

- Create a new task
- Display list of all tasks
- Delete a completed task
- Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, we need to create routes that will define various endpoints that the To-do app will depend on.

- Lets create Routes folder with the command below:

  `mkdir routes`
  
- Change directory to routes folder:

  `cd routes`
  
- create a file api.js with the command below:

  `touch api.js`
  
- Open the file with the command below:

  `vim api.js`
  
- In the blank file paste text below and save the file:

```
  const express = require ('express');
  const router = express.Router();

  router.get('/todos', (req, res, next) => {

  });

  router.post('/todos', (req, res, next) => {

  });

  router.delete('/todos/:id', (req, res, next) => {

  })

  module.exports = router;
  
```

#### Models
Since the App will be interacting with a NOSQL database in this case Mongodb, We need to create a model. A model is blueprint for a collection in a NOSQL database.
mongoose is an Object Document Mapper(ODM). It esentially eases interaction with Mongodb. It will be used to define our model.

- Change directory back Todo folder with cd .. and install Mongoose with the command below:

  `npm install mongoose`
  
- Create a new folder models :

  `mkdir models`
  
- Change directory into the newly created ‘models’ folder with:

  `cd models`
  
- Inside the models folder, create a file and name it todo.js:

  `touch todo.js`
  
- Open the file created with vi todo.js then paste the code below in the file:


```
   const mongoose = require('mongoose');
   const Schema = mongoose.Schema;

  //create schema for todo
  const TodoSchema = new Schema({
  action: {
  type: String,
  required: [true, 'The todo text field is required']
  }
  })

  //create model for todo
  const Todo = mongoose.model('todo', TodoSchema);

  module.exports = Todo;
  
```

- Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.

  In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit
  
  
```
   const express = require ('express');
   const router = express.Router();
   const Todo = require('../models/todo');

   router.get('/todos', (req, res, next) => {

   //this will return all the data, exposing only the id and action field to the client
   Todo.find({}, 'action')
   .then(data => res.json(data))
   .catch(next)
   });

   router.post('/todos', (req, res, next) => {
   if(req.body.action){
   Todo.create(req.body)
   .then(data => res.json(data))
   .catch(next)
   }else {
   res.json({
   error: "The input field is empty"
   })
   }
   });

   router.delete('/todos/:id', (req, res, next) => {
   Todo.findOneAndDelete({"_id": req.params.id})
   .then(data => res.json(data))
   .catch(next)
   })

   module.exports = router;
   
```

#### MongoDB Database

We need a database where we will store our data. For this we will make use of mongodb Atlas. Atlas provides MongoDB database as a service solution (DBaaS),
Follow the installation guide and make sure you whitelist your database to allow connection from any Ip address by setting it to: 0.0.0.0/0.

In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.

- Create a file in your Todo directory and name it .env.

  ```
  touch .env
  vi .env
  ```
- Add the connection string to access the database in it, just as below:

  `DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`
  
Now we need to update the index.js to reflect the use of .env so that Node.js can connect to the database.

- Delete the content of index.js, paste the text below and save the file:

  ```
     const express = require('express');
     const bodyParser = require('body-parser');
     const mongoose = require('mongoose');
     const routes = require('./routes/api');
     const path = require('path');
     require('dotenv').config();

     const app = express();

     const port = process.env.PORT || 5000;

     //connect to the database
     mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
     .then(() => console.log(`Database connected successfully`))
     .catch(err => console.log(err));

     //since mongoose promise is depreciated, we overide it with node's promise
     mongoose.Promise = global.Promise;

     app.use((req, res, next) => {
     res.header("Access-Control-Allow-Origin", "\*");
     res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
     next();
     });

     app.use(bodyParser.json());

     app.use('/api', routes);

     app.use((err, req, res, next) => {
     console.log(err);
     next();
     });

     app.listen(port, () => {
     console.log(`Server running on port ${port}`)
     });
  ```

- Start your server using the command:

  `node index.js`
  
- You should soething like the screenshot below if done successfully:

  ![mongodb](https://user-images.githubusercontent.com/52359007/165506464-7d7bd7ef-5735-4dd1-a07b-3bb0d2aa94c3.PNG)
  
- Postman is a tool that is used to manually test Api endpoints. You should see something like the screenshot below if our endpoints work perfectly:

  ![Screenshot (7)](https://user-images.githubusercontent.com/52359007/165507309-1b5d356e-5753-4a08-b420-82d71d6a795d.png)
  
  
  
### Step 2 – Frontend creation
Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.

- In the same root directory as your backend code, which is the Todo directory, run:

  `npx create-react-app client`
  
#### Running a React App

- Install concurrently with the command below:

  `npm install concurrently --save-dev`
  
- Install nodemon with the command below:

  `npm install nodemon --save-dev`

- In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.

  ```
     "scripts": {
     "start": "node index.js",
     "start-watch": "nodemon index.js",
     "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
     },
     
  ```
  
#### Configure Proxy in package.json

- Change directory to ‘client’ with the command below:

  `cd client`

- Open the package.json file:

  `vi package.json`
  
- Add the key value pair in the package.json file "proxy": "http://localhost:5000"
  The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling     the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos
  
- Now, ensure you are inside the Todo directory, and simply do:

  `npm run dev`
  
  This should start the app on port 3000

#### Creating your React Components

- From your Todo directory run the command below:
  
  `cd client`
  
- move to the src directory

  `cd src`
  
- Inside your src folder create another folder called components:

  `mkdir components`
  
- Move into the components directory with the command below:

  `cd components`
  
- Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js:

  `touch Input.js ListTodo.js Todo.js`
  
- Open Input.js file:

  `vi Input.js`

- Copy and paste the code below:

  ```
      import React, { Component } from 'react';
      import axios from 'axios';

      class Input extends Component {

      state = {
      action: ""
      }

      addTodo = () => {
      const task = {action: this.state.action}

      if(task.action && task.action.length > 0){
       axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
           }
         })
         .catch(err => console.log(err))
      }else {
      console.log('input field required')
     }

   }

     handleChange = (e) => {
    this.setState({
     action: e.target.value
   })
   }

     render() {
    let { action } = this.state;
    return (
    <div>
    <input type="text" onChange={this.handleChange} value={action} />
    <button onClick={this.addTodo}>add todo</button>
    </div>
       )
       }
    }

   export default Input
  ```


- Install Axios: A http client using the commands below:

  `cd ..; cd ..; npm install axios;`

  `


  


  





  
  
  

  


  
  
