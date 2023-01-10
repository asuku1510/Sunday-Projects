# # WEB STACK IMPLEMENTATION (LAMP STACK)
I Created an AWS account and an EC2 instance

Generated a Private key and logged into the AWS server through Visual studio code Terminal

## STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL
I installed apache with this commands

#update a list of packages in package manager:
 sudo apt update

#run apache2 package installation:
 sudo apt install apache2
 
## STEP 2 — INSTALLING MYSQL
Ran sudo apt install mysql-server

Ran sudo mysql_secure_installation, to install secured Mysql

Ran  sudo mysql -p
![Mysql](https://user-images.githubusercontent.com/92901887/173271831-cb4502d5-558d-4381-bfe1-826c26836c00.PNG)

## STEP 3 — INSTALLING PHP
Ran sudo apt install php libapache2-mod-php php-mysql db

## STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE
Ran sudo mkdir /var/www/projectlamp to create directory for projectlamp

 Ran  sudo chown -R $USER:$USER /var/www/projectlamp to  assign ownership of the directory with your current system user
 
 Ran sudo vi /etc/apache2/sites-available/projectlamp.conf to edit the config file
 
 Ran sudo a2ensite projectlamp to enable virtual host
 
 Ran sudo a2dissite 000-default to disable apache default website
 
 Ran sudo apache2ctl configtest to check syntax error
 ![To check syntax error](https://user-images.githubusercontent.com/92901887/173274823-b12d31cf-62f8-4e3b-afe5-878802579441.PNG)
 
 Ran sudo systemctl reload apache2 to reload apache so the changes take effect
 
 Lunched the website from the browser
 
 
 ![Website](https://user-images.githubusercontent.com/92901887/173276191-5dc39697-619b-4b46-a28d-c212d4f1be9c.PNG)
 
 # # STEP 5 — ENABLE PHP ON THE WEBSITE
 
 Ran sudo vim /etc/apache2/mods-enabled/dir.conf to change the order in which the index.php file is listed with the Derectory index
 
 ![Config directory](https://user-images.githubusercontent.com/92901887/173276836-f3a2f832-0a25-4932-a295-8faf7a158c0e.PNG)
 
 Ran sudo systemctl reload apache2 to reload Apache so that changes take effect
 
 Lunched the website
 
 ![php website](https://user-images.githubusercontent.com/92901887/173277162-7f2b029f-891d-44ae-83cc-88c9fba2b8a9.PNG)




 
 
 
 
 

 
 
 

