## Create Clusters in private subnets with ELB
One of the best practices of building highly available, robust and fail-tolerant systems is to build redundancy. 
Elastic Load Balancing (ELB) automatically distributes incoming application traffic across multiple targets,

    Create 2 instances with Apache httpd and hello world HTML, register them with ELB - only private IP
    Create an app ELB with external (public) IP
    Test that you can see the page by going to the ELB’s public DNS (not IP!)
    Stop one of the instances and see if you can still see the webpage


## Step1 : Create a VPC
If no exists, create a VPC and specify the IPv4 CIDR block (e.g 10.0.0.0/16)
## Step 2 : Create 2 subnets in 2 differents Availability Zones
Within the VPC created above, create 2 subnets in **2 differents Availability Zones** , assign them IPv4 CIDR block (e.g 10.0.1.0/24 and 10.0.2.0/24)
## Step 3 : Create Internet Gateway
An internet gateway is a virtual router that connects a VPC to the internet. Here we create a new one, specify the name and attach it to the VPC created above.
## Step 4: Create Route Tables
A route table specifies how packets are forwarded between the subnets within your VPC, the Internet, and your VPN connection.
Create a route table :
- configuration (at least): 
  - Target local (Destination : VPC )
  - Target Internet Gateway (Destination : internet(0.0.0.0/0)
  - Associate this route table to the 2 subnet created above
## step 5 : Configuration of Security Groups
A security group acts as **a virtual firewall** that controls the traffic for one or more instances. When an instance is launched, is associated to one or more security groups. Each security group has rules that allow traffic to or from its associated instances.
**Create  a security group which allow at least SSH and HTTP**
## step 6 :  Launch instances bounded with Public IPs
Because the objectif is to using instances which don’t have public IPs. However this instances they won’t be able to fetch yum update and source code from the internet. Then we are just going to create instances with a public IP first, this we'll allow to verify that the web server is running, and then create an image of those instances.
- Create an instance in each subnet created above (I have used linux, t2.micro)
- Enable Auto-Assign public IP
- User Data which creates an HTML page served by Apache httpd web server (link)
- Associated the Security group created above( an open one that allows at least SSH and HTTP(80))
## Step 7 : Test the Public IP configuration
Copy the IPv4 Public IP address of each instance  Paste the address in the browser and press enter. We see: Hello Fred for the first instance and Bye Bye Fred for the second One (or whatever HTML you put on the instances).
This step validates the configuration of the httpd server
## step 8 : Create Images
After the validation of httpd server we create an image of each instance :
- Create an image : select an instance **-> Actions ->Image ->Create image **
Once the images are created from your public instances, the images are now available in **My AMIs tab**
## step 9: Launch instances bounded with Private IPs
Now, we are going to create instances with private IPs only from the images created above (step 8).
The two instances are launched in the 2 differents Available zones with :
- Auto-Assign public IP : **Desabled**
- User Data: nothing (default)
- Security Group (at least allow SSH and HTTP (80))
Once these 2 instances launched we'll see them without Public IPs, just with only pirivate IPs
## Step 10 : Create ELB
Elastic Load Balancing (ELB) is a load-balancing service that automatically distributes incoming application traffic and scales resources to meet traffic demands. 
To create a one: we go on AWS web console and navigate to the EC2 management console. In the left sidebar menu, click on Load Balancers, then press a blue button “Create Load Balancer” from the top menu.
- Type of ELB : Here we choose an **Application ELB**
- Configure Load Balancer:
    - enter name (arbitrary)
    - Select internet-facing
    - seelect IPv4
    - Listeners: HTTP port 80 (**optionally, HTTPS 443 - but need a key and certificate**)
    - VPC: same as your instances
    - Availability zones: Select the 2 Availability where are the instances
    - Tags: (optionally)
    - Configure Security Settings by clicking Next if you didn’t select HTTPS 
    - Configure Security Groups, **select a group(s) with at least HTTP 80 and SSH 22**.
    - Configure Routing, select “New target group” from the Target group drop down and enter name (arbitrary).
    - Register Targets, add 2 Apache httpd instances to the target group.
    - Review, check all the configs and then click on create button (blue). 

The important thing here is to have two instances from different Available Zones under the same ELB (in the same target group):
## Step 11 : Test Configuration
Now we are going to test the ELB.
Copy the ELB’s DNS name from the Description tab in the ELB list view. Paste the address in the browser and press enter.
![Test link : Screenshot](https://github.com/fredtw/images/blob/master/ELB_2PrivateSubnets.png)
