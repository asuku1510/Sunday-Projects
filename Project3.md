# #(STEP 5) PROJECT 3: MERN STACK IMPLEMENTATION

# STEP 1 – BACKEND CONFIGURATION
- update ubuntu
sudo apt update

- Upgrade ubuntu

sudo apt upgrade


- Lets get the location of Node.js software from Ubuntu repositories

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

- Install Node.js on the server

sudo apt-get install -y nodejs

-Application Code Setup, make dir Todo and initialize project
mkdir Todo

npm init

- Install ExpressJS

npm install express

- create a file index.js

touch index.js

- Install the dotenv module
npm install dotenv

- Open index.js

vim index.js and paste the config
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

- Create routes

mkdir routes

- create api.js file inside routes dir
touch api.js
vim api.js
Copy the code into api.js

- MODELS creating Mongodb

npm install mongoose

mkdir models

- create a file todo.js inside models and paste the code inside it

- Update routes from api.js in routes directory
vim api.js 


- module.exports = router;
- Creating Mongodb
Sign up and  Created a datbase MYFirstDb
Created .env using touch .env 
Copied the connection string 
vi .env and paste the connection string from the db
Copied the connection string

- Create backend
Post, Get and delete using Postman.

![post1](https://user-images.githubusercontent.com/92901887/175466088-f7e88124-3fcc-481c-b174-8ea7c59024a1.PNG)


![get1](https://user-images.githubusercontent.com/92901887/175466186-aae3c023-b348-412d-be68-3a387d4c799b.PNG)



# # STEP 2 – FRONTEND CREATION
- create-react-app
- 
 npx create-react-app client

- Install dependencies concurrently.

npm install concurrently --save-dev

- Install nodemon for monitoring server

npm install nodemon --save-dev

In Todo folder open the package.json file and update the scripts

- Configure Proxy in package.json in client directory

vi package.json and 

add "proxy": "http://localhost:5000". to the file

run:  'npm run dev' in Todo directory

![react1](https://user-images.githubusercontent.com/92901887/175479476-1ab473c9-4909-4e06-b284-c77d00f2ce81.PNG)

![port 5000 open](https://user-images.githubusercontent.com/92901887/175465774-ceea3705-168a-4e66-bc91-3cde6084a24f.PNG)

![react app on port 3000](https://user-images.githubusercontent.com/92901887/175471867-71546b23-172b-4832-89a3-47f1c42874ce.PNG)

creating your react component

- mkdir components in src directory inside client directory
cd component

touch Input.js ListTodo.js Todo.js
I Open the files Vim Input.js and paste the appropriate codes.

- Install Axios
npm install axios
- Go to Component directory and open  Vim ListTodo.js  and Todo.js and paste the appropriate codes

- Go to App.js
vi App.js in src folder and paste the code
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;

- In the scr directory open vi App.css and paste the code
- Go to Todo directory and run
npm run dev

![webpage display](https://user-images.githubusercontent.com/92901887/175480066-6538f3fe-b970-43c8-89e2-87e29968e2f5.PNG)

# # Blockers
- When i opened my index.js and othe files in vscode using Vim or Vi, some lines in the codes was commented out causing me to have syntax errors.

- I had to use nano instead of vi and also switched to Gitbash and the issue was resolved.









