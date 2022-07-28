# Step1: Prepare a Web Server provision an EC2 server and mount the volumes
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
- sudo lvcreate -n apps-lv -L 14G webdata-vg
- sudo lvcreate -n logs-lv -L 14G webdata-vg

9. Verify that your Logical Volume has been created successfully by running 
- sudo lvs

10. Verify the entire setup

- sudo vgdisplay -v #view complete setup - VG, PV, and LV
- sudo lsblk 

11. Use mkfs.ext4 to format the logical volumes with ext4 filesystem
- sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
- sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

13. Create /var/www/html directory to store website files
- sudo mkdir -p /var/www/html
14.  Create /home/recovery/logs to store backup of log data
- sudo mkdir -p /home/recovery/logs

15. Mount /var/www/html on apps-lv logical volume
- sudo mount /dev/webdata-vg/apps-lv /var/www/html/

16. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
- sudo rsync -av /var/log/. /home/recovery/logs/

17. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)
- sudo mount /dev/webdata-vg/logs-lv /var/log

18.  Restore log files back into /var/log directory

- sudo rsync -av /home/recovery/logs/. /var/log

19.  Update /etc/fstab file so that the mount configuration will persist after restart of the server.

- sudo blkid

Use the UUID to update /etc/fstab

/dev/mapper/webdata--vg-apps--lv: UUID="3b5fe771-adf0-4735-bd68-1afe2ce0b38d" BLOCK_SIZE="4096" TYPE="ext4"
/dev/mapper/webdata--vg-logs--lv: UUID="fcdb5d84-c4be-47f3-bfc0-90e10d652fb9" BLOCK_SIZE="4096" TYPE="ext4"

- sudo vi /etc/fstab

Test the setup and reload daemon

- sudo mount -a
- sudo systemctl daemon-reload

# Step 2 — Created a second EC2 server for the Database and follow same process as the Web app server to prepare the volume,Prepare the Database Server

- apps-lv create db-lv and mount it to /db

# Step 3 — Install WordPress on your Web Server EC2

1 Install wget, Apache and it’s dependencies

2 sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

3 Start Apache

4 sudo systemctl enable httpd
5 sudo systemctl start httpd

6 To install PHP and it’s depemdencies

- sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
- sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
- sudo yum module list php
- sudo yum module reset php
- sudo yum module enable php:remi-7.4
- sudo yum install php php-opcache php-gd php-curl php-mysqlnd
- sudo systemctl start php-fpm
- sudo systemctl enable php-fpm
- sudo setsebool -P httpd_execmem 1

7 Restart Apache

- sudo systemctl restart httpd

8 Download wordpress and copy wordpress to var/www/html

  - mkdir wordpress
  - cd   wordpress
  - sudo wget http://wordpress.org/latest.tar.gz
  - sudo tar xzvf latest.tar.gz
  - sudo rm -rf latest.tar.gz
  - sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  - sudo cp -R wordpress /var/www/html/
9 Configure SELinux Policies

  - sudo chown -R apache:apache /var/www/html/wordpress
  - sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
  - sudo setsebool -P httpd_can_network_connect=1

# Step 4 — Install MySQL on your DB Server EC2

  - sudo yum install mysql-server

- Restart and enable mysql service
- sudo systemctl restart mysqld
- sudo systemctl enable mysqld

Step 5 — Configure DB to work with WordPress
- sudo mysql
- CREATE DATABASE wordpress;
- CREATE USER `sunny`@`172-31-19-85` IDENTIFIED BY 'jude1510';
- GRANT ALL ON wordpress.* TO 'sunny'@'172.31.37.230';
- FLUSH PRIVILEGES;
- SHOW DATABASES;
- exit
  
  ![db connection from web worldpress](https://user-images.githubusercontent.com/92901887/181630190-0e982e26-6611-4891-a07e-8fabe613ddbd.PNG)


# Step 5 - Go into /var/www/html/wordpress  on the webserver
  - sudo vi wp-config.php, 
  - edit the database parameters

# Step 6- Launch the webpage on the browser
  ![wordpress page1](https://user-images.githubusercontent.com/92901887/181628748-1707201f-89a1-4e3c-9a00-3df910e426af.PNG)

