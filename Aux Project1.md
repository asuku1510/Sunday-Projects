# #  AUX PROJECT 1: SHELL SCRIPTING

1. Creat the project folder called shell


mkdir shell


2. Create a csv file name names.csv

touch names.csv  

vim names.csv and add 20 names

3. create group developers
 
sudo groupadd developers

4. Creating script
touch onboarding.sh

Giving execute permission to the onboarding.sh  file
Sudo Chmod +X onboarding.sh


Vi onboarding.sh and insert the script below

#!/bin/bash
#Darey.io AUX PROJECT 1
#This script is written by Sunday Asuku

#!/usr/bin/bash

names_path="./names.csv"
group_name="developers"
name=$USER
key_pair_name=key_pair

Generate key-pair

ssh-keygen -f $key_pair_name
if cat /etc/group | grep $group_name; then
echo "Group exists"
else
sudo groupadd $group_name
fi

for name in `cat $names_path`
do
  if cat /etc/passwd | grep $name; then
   echo "${name} user exists"
    else
        sudo useradd -s /bin/bash -G $group_name -m -d /home/$name -U $name
        sudo mkdir -p /home/$name/.ssh
        sudo cp ${key_pair_name}.pub /home/$name/.ssh/authorized_keys
fi
done

Running the script
5. ./onboarding.sh

6. Display Users created

- ![users in home created by the script](https://user-images.githubusercontent.com/92901887/178162441-787c75f3-a02b-49e8-bbb1-8d0d58d524b3.PNG)



Login with one of the user for test

7.  ssh -i key.pem Albert@3.144.158.61

![login on with a user Albert](https://user-images.githubusercontent.com/92901887/178162510-7b869bb3-59a6-4c6a-87b5-023ae1ffd439.PNG)

Testing with another user

![testing a user created](https://user-images.githubusercontent.com/92901887/178162532-948061e0-783a-47f6-abff-8b1c1a13fed9.PNG)

