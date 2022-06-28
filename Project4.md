# #  (STEP 10) PROJECT 4: MEAN STACK IMPLEMENTATION

- Step1: installing NodeJs

sudo apt update
update and upgrade


- add certificates
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -

- Install NodeJs

sudo apt install -y nodejs

- Step 2: Install MongoDB

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

- Install MongoDB

sudo apt install -y mongodb

- Start the server

sudo service mongodb start

- and verify service running

sudo systemctl status mongodb

- Install body-parser package

sudo npm install body-parser

- Create a folder called Books

mkdir Books && cd Books

- Initialize npm pojects


npm init

- add a file name server.js and copy and paste the web server code into the file

# # INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER
-sudo npm install express mongoose

- Create a folder apps, create a file routes.js in apps folder and paste the code into it

mkdir apps && cd apps

- Create a folder called models and create a file books.js inside models, open books.js and paste the code

Step 4 – Access the routes with AngularJS

Goto Books folder, add a file named script.js, paste the code(Controller configuration defined)

-  mkdir public && cd public
vi script.js

-  create a folder named index.html in public folder copy and paste the code

vi index.html

- change directory to Books dir and start the server
node server.js

curl -s http://localhost:3300, goto aws and add 3300 to inbound role

![webpage display](https://user-images.githubusercontent.com/92901887/176089067-323ad53f-b9fe-44b1-815d-302a7aff9995.PNG)


## Blocker

I was having error installing the mango db

Found out that i was running on Ubuntu version 22_04, I stand up another instance and installed ubuntu vesion 20.04

![db error on ubuntu 22 04](https://user-images.githubusercontent.com/92901887/176089743-66866199-7b01-4972-9a1b-55dbc5049b94.PNG)








