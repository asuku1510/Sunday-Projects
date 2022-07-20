# IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS).

1. Create and configure two Linux-based virtual servers (EC2 instances in AWS).

   Run On the mysql server
   sudo apt install mysql-server -y

   sudo systemctl enable mysql

   Run On mysql client
   sudo apt install mysql-client -y
   
 2. Create Database, user in mysql database and grant access
    
    CREATE USER 'test'@'%' IDENTIFIED BY 'jude1510';
    
    CREATE DATABASE Project5_db;
    
    GRANT ALL ON Project5_db.* TO 'user'@'%' WITH GRANT OPTION;

   
 3. Create inbound security rules for port 3306 on mysql server
 
   ![security settings on mysql server](https://user-images.githubusercontent.com/92901887/180066479-a3d45f40-c6dc-46f9-8480-9739f02f25fb.PNG)

 4. configure MySQL server to allow connections from remote hosts.

    sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
   
   ![Bind setting](https://user-images.githubusercontent.com/92901887/180067164-0cbd302c-ca85-4d0a-8869-06702fde426b.PNG)
   
 5. From mysql client Linux Server connect remotely to mysql server Database Engine and check that you can connect and perform SQL queries
 
     sudo mysql -u sunny -h 172.31.17.171 -p
     
     ![login to mysql server from client and running a query](https://user-images.githubusercontent.com/92901887/180069252-4aa8bf10-846d-4ab3-998b-58f4c8ba5691.PNG)

  6. Blocker session

     Error in a command for remote mysql login  sudo mysql -u sunny-h 172.31.17.171 -p
     
     Corrected it to sudo mysql -u sunny -h 172.31.17.171 -p and was able to complete the project

