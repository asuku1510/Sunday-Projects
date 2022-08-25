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
