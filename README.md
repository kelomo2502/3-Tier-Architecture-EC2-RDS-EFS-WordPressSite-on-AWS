# WordPressSite-on-AWS

## VPC setup

- IP address range definition

## VPC Creation

- To create a VPC, log into AWS console and search for VPC from the services balancer
![Locating the VPC services](/images/1.locating_VPVservice_AWS.png)
- Click on create VPC button and give your VPC a descriptive name
![Create VPC](/images/2.Create-VPC.png)
- Choose create VPC only from the options
- Give the VPC a descriptive name. I have named this one "DigitalBoostVPC" which is the name of the company
- Choose IPV4 CIDR manual input
- Keep tenancy as default
- While creating the VPC, keep in mind the number of IP ranges that would be suitable for purpose. in this case, i am using a VPC with CIDR block of 10.0.0.0/26 with subnets CIDR 10.0.0.0/28, 10.0.0.16/28 and 10.0.0.32/28
- Also take into account the number of subnets required for the project. In this case, I have will be using 3 subnets in 2 different availability zones. one private and two public. The private subnet will host instances with database and the app, while the public subnets will be used for the load balancer and auto-scling group
- Let's create our subnets by locating the subnets tab on the left corner under the vpc page
![Locating subnets menu](/images/3.subnet-menu.png)
- Click on Create subnet
- Give your subnets descriptive names, in this case I will be naming them PrivateSubnet, PublicSubnet1 and PublicSubnet2. Also remember to specify the CIDR block for each of the subnet
- After filling in all the subnets details, click on create subnets and you have successfully created the subnet
- Let's create an internet gateway which will be attached to the route table of public subnet, while the route table of private subnet will be without internet gateway
- On the left side of the VPC, locate the internet Gateway tab
![Locating IGW tab](/images/4.Internet-gatwayTab.png)
- Click on Create Internet Gateway
![Create Internet Gateway](/images/Create-internet-gatewat.png)
- Give it a descriptive name
- Click on create internet gateway
![Internet gateway setup](/images/setup-igw.png)
- Attache the Internet gateway to your VPC by selecting it and clicking on the action tab and select attach to VPC then select your VPC
- At this point you have successful created a VPC with public and private subnets

## Route table configuration

Correct configuration of route table for each subnets

- To designate each of the subnets as private or public subnet, we would need to configure two route tables, one with internet gateway while the other will be without internet gateway
- Make the route table public editing its routes and giving it acces to the internet via the internet gateway
![Configuring the route table for internet access](/images/7.configuring-route-table.png)

## Public and Private subnets with NAT gateway

- Public subnet setup (Proper setup public subnet for resource accessible from the internet)
- Private subnet setup (Successful creation of private subnet for resources with no direct internet access)
- Nat Gateway configuration (Correct configuration of a NAT Gateway for private subnet internet access)

## AWS MYSQL RDS Setup

- RDS instance creation (Proper creation of Amazon RDS instance with MYSQL engine )
- Security group configuration

## Wordpress RDS connection

- Successful connection of wordpress to RDS database
  
## EFS connection for wordpress files

- EFS file system creation (Proper creaton of an EFS file system)
- Mounting EFS (Successful mounting of the EFS file system on a wordpress instance)
- Wordpress configuration (Correct wordpress configuation to use the shared flie system)

## Application load balancer and auto scaling

- ALB creation (Successful creation of application load balancer)
- Listener rule configuration (Proper configuration of listener rule for routing traffic to instances)
- Intergartion with auti scaling
