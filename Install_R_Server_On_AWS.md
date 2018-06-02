
## Install R Server on AWS Cloud infrastructure
This page is largely inspired by the [Running R on AWS article](https://aws.amazon.com/fr/blogs/big-data/running-r-on-aws/)

## Step 1 Create a VPC
This Rserver will be set in a public subnet , then if not yet created, the first steps include providing :
- VPC with a /16 IPv4 CIDR block
  - configure the IPv4 CIDR block (e.g : 10.0.0.0/16)
- Internet Gateway (attaches it to the VPC above)
- Public subnet with a /24 IPv4 CIDR block
  - configure the IPv4 CIDR block (e.g : 10.0.1.0/24)
- Route table
  - Route ( target : local (e.g : 10.0.0.0/16; the VPC IPv4 CIDR), and Internet gateway (e.g 0.0.0.0/0)
  - subnet association (add the public subnet created above)
  
## Step : Launch an EC2



