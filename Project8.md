# Reference project7 and make sure the 2 web server are running

# Configure apache as load balancer

- Step1: Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this

- Step2:  Open TCP port 80 on Project-8-apache-loadbalancer by creating an Inbound Rule in Security Group.

- Step3:  Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers
- #Install apache2
- sudo apt update
- sudo apt install apache2 -y
- sudo apt-get install libxml2-dev

# Enable following modules:
- sudo a2enmod rewrite
- sudo a2enmod proxy
- sudo a2enmod proxy_balancer
- sudo a2enmod proxy_http
- sudo a2enmod headers
- sudo a2enmod lbmethod_bytraffic

# Restart apache2 service
- sudo systemctl restart apache2

Step4: Configure load balancing

- sudo vi /etc/apache2/sites-available/000-default.conf

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

# Restart apache server
- sudo systemctl restart apache2
# Configure Local DNS Names Resolution
- sudo vi /etc/hosts
  
![Dns name resolution](https://user-images.githubusercontent.com/92901887/190314830-aa195ee1-d6e9-4ff1-8ec1-26e48e21a1c2.PNG)
  
 # Test the load balancer
  - http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php
  ![load balancer](https://user-images.githubusercontent.com/92901887/190315842-d18e1638-fc88-4b69-9087-32187758e72c.PNG)

