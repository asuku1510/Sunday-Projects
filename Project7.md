# Step1: Prepare a Web Server provision an EC2 RHEL server and mount the volumes
1. Use df -h command to see all mounts and free space on your server

2. Use gdisk utility to create a single partition on each of the 3 disks

- sudo gdisk /dev/xvdf
- type n
- press enter trice to select default
- type 8e00 or 8300
- p to preview
- w to write
- y to complete the process

3. Install lvm2 package using 
 - sudo yum install lvm2. 
Run 
- sudo lvmdiskscan command to check for available partitions.

4. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
- sudo pvcreate /dev/xvdf1
- sudo pvcreate /dev/xvdg1
- sudo pvcreate /dev/xvdh1

or
- sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

5. Verify that your Physical volume has been created successfully by running 
- sudo pvs

6. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

- sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1


7. Verify that your VG has been created successfully by running 
- sudo vgs

8. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. 
NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
- sudo lvcreate -n apps-lv -L 6G webdata-vg
- sudo lvcreate -n logs-lv -L 6G webdata-vg
- sudo lvcreate -n opt-lv -L 6G webdata-vg

9. Verify that your Logical Volume has been created successfully by running 
- sudo lvs

10. Verify the entire setup

- sudo vgdisplay -v #view complete setup - VG, PV, and LV
- sudo lsblk 
![3 partitions displayed](https://user-images.githubusercontent.com/92901887/181637589-6cc2e295-931a-4cbf-bf01-279ad3801683.PNG)

11. Use mkfs.ext4 to format the logical volumes with ext4 filesystem
- sudo mkfs -t xfs /dev/webdata-vg/apps-lv
- sudo mkfs -t xfs /dev/webdata-vg/logs-lv
- sudo mkfs -t xfs /dev/webdata-vg/opt-lv

13. Create /var/www/html directory to store website files
- sudo mkdir -p /mnt/apps
14.  Create /home/recovery/logs to store backup of log data
- sudo mkdir -p /mnt/logs
15. Create /var/www/html directory to used by jenkins server in project 8
16. sudo mkdir -p /mnt/opt

17. Mount /mnt/apps/ on apps-lv logical volume
- sudo mount /dev/webdata-vg/apps-lv /mnt/apps/

18. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
- sudo rsync -av /var/log/. /home/recovery/logs/

19. Mount /mnt/logs on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)
- sudo mount /dev/webdata-vg/logs-lv /mnt/logs

20. Mount /mnt/opt/ on apps-lv logical volume
- sudo mount /dev/webdata-vg/apps-lv /mnt/opt/

21.  Update /etc/fstab file so that the mount configuration will persist after restart of the server.

- sudo blkid

Use the UUID to update /etc/fstab

- UUID=eaa1f38e-de0f-4ed5-a5b5-2fa9db43bb38       /       xfs     defaults        0       0
- UUID=be3fa4bf-587c-49bb-b62b-7a9415b43bd6       /mnt/apps  xfs  defaults        0       0
- UUID=1d2ea382-70fa-478d-9f4e-ed30b9127e65       /mnt/opt  xfs   defaults        0       0
- UUID=3e7ae900-6639-4914-aa04-7ffe87f89355       /mnt/logs xfs   defaults        0       0

- sudo vi /etc/fstab

Test the setup and reload daemon

- sudo mount -a
- sudo systemctl daemon-reload

# Step 2 — Install NFS server, configure it to start on reboot and make sure it is up and running
1. install NFS server
- sudo yum -y update
- sudo yum install nfs-utils -y
- sudo systemctl start nfs-server.service
- sudo systemctl enable nfs-server.service
- sudo systemctl status nfs-server.service

2. Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:
- sudo chown -R nobody: /mnt/apps
- sudo chown -R nobody: /mnt/logs
- sudo chown -R nobody: /mnt/opt
- sudo chmod -R 777 /mnt/apps
- sudo chmod -R 777 /mnt/logs
- sudo chmod -R 777 /mnt/opt
- sudo systemctl restart nfs-server.service

3. Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):
- sudo vi /etc/exports
- /mnt/apps 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
- /mnt/logs 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
- /mnt/opt 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)

4. In order for NFS server to be accessible from your client, you must also open following inbound rule ports: TCP 111, UDP 111, UDP 2049, TCP 2049

# Step 3 : Configure the database server
1. Install mysql server
- sudo yum install mysql
- sudo mysql
2. Create a database and name it tooling
- CREATE DATABASE tooling;
3. Create a database user and name it webaccess
- create user 'webaccess'@'%' identified with mysql_native_password by 'jude1510';
4. Grant permission to webaccess user on tooling database and flush priveleges
- grant all privileges on tooling.* to 'webaccess'@'%';
- flush privileges
5. Edit band address to 0.0.0.0 and  restart mysql
- sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
- restart mysql
- sudo systemctl restart mysql
# step 4 : Install and configure 2 webservers NFS client from the EC2 stand up 
1. install NFS client utility
- sudo yum install nfs-utils nfs4-acl-tools -y

2. Mount /var/www/ and target the NFS server’s export for apps
- sudo mkdir /var/www
- sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www

3. Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:

- sudo vi /etc/fstab

- enter : 172.31.32.255:/mnt/apps /var/www nfs defaults 0 0 

4. Install Remi’s repository, Apache and PHP
- sudo yum install httpd -y

- sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

- sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

- sudo dnf module reset php

- sudo dnf module enable php:remi-7.4

- sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

- sudo systemctl start php-fpm

- sudo systemctl enable php-fpm

- sudo setsebool -P httpd_execmem 1

5. Install git on 172.31.39.6: 
- Sudo yum install git
- and fork the tooling source code from Darey.io Github Account to your Github account
- git clone https://github.com/darey-io/tooling
- ls
- cd tooling
- ls
6. Deploy the tooling website's code to the webserver. Ensure that the html folder from the repocitory is deployed to /var/www/html
- cd ..
- mv tooling/*
- ls 
- ls tooling
- sudo rm -r tooling
- mv ./html/* .
- ls
 7. Disable SELinux and restart the apache server
 -sudo setenforce 0
- sudo vi /etc/sysconfig/selinux
- SELINUX=disabled                                                                    
- restart the appache httpd server
- sudo systemctl restart httpd
 
8. install mysql on the web servers
- sudo yum install mysql
- mysql -u webaccess -pjude1510 -h 172.31.25.239 tooling < tooling-db.sql
- mysql -h <databse-pr -u <db-username> -p <db-pasword> < tooling-db.sql
                                                                        
9. Open port 80 on the webserver
                                                                        
10. Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the websute with myuser user. 
 ![project7 web](https://user-images.githubusercontent.com/92901887/186587840-c01a182b-1fd4-48bf-9276-01698a5728f5.PNG)

 ![proj7 login](https://user-images.githubusercontent.com/92901887/186587626-fa42498e-c501-4cb7-82c3-4461b70ff18a.PNG)

                                                                        






