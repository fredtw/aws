
## Install R Server on AWS Cloud infrastructure
This page is largely inspired by the [Running R on AWS article](https://aws.amazon.com/fr/blogs/big-data/running-r-on-aws/)

## Step 1: Create a VPC
This Rserver will be set in a public subnet , then if not yet created, the first steps include providing :
- VPC with a /16 IPv4 CIDR block
  - configure the IPv4 CIDR block (e.g : 10.0.0.0/16)
- Internet Gateway (and attaches it to the VPC above)
- Public subnet with a /24 IPv4 CIDR block
  - configure the IPv4 CIDR block (e.g : 10.0.1.0/24)
- Route table
  - Route ( target : local (e.g : 10.0.0.0/16; the VPC IPv4 CIDR), and Internet gateway (e.g 0.0.0.0/0)
  - subnet association (add the public subnet created above)
  
## Step 2 : Launch an EC2
This configuration has selected an **Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type - ami-ca0135b3** AMI with t2.micro as an instance type
- **Instance configuration**
  - Network : VPC configured in step 1
  - Subnet : the public subnet configured in step 1
  - Auto-assign Public IP : Enable
  - Advanced Details : add **user data** [the link for a script for installation after instance start](https://github.com/fredtw/aws/blob/master/RserverScript.txt); this script installs R, RServer, Shiny and Shiny Server ( Just remender to [check for the latest versions of RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) and make an update if required as well as the username and password configuration.
 - **Add tags**
    - Name : RServer
 - **Security Group** 
 This acts as a firewall that controls the traffic for this instance, here will oen port 22 for ssh communications and port 8787 for Rserver
  - configure the security groupe:
      - Name (e.g: Rserver_SG)
      - Description (e.g :R Server Security Group)
  - Add Security group rules (inbound):
      - Type: SSH, Protocol : TCP, Port Range: 22 Source : Anywhere (0.0.0.0,::/0)
      - Type: Custom TCP Rule, Protocol : TCP, Port Range: 8787 Source : Anywhere (0.0.0.0,::/0)
   - After this configuration , review and launch this instance; Selecting an existing key pair or create a new one
   
## Step 3 : connect to the Rserver

After your EC2 instance is running
- First connect to the instance through ssh
- Now you can connect to the Rserver using the browser (e.g of URL : http://<the IPV4>:8787 ) [example of a connection](https://github.com/fredtw/images/blob/master/ConnectToRServerOnAWS.jpg)
 


