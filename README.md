# WordPressSite-on-AWS

## VPC setup

IP address range definition

- We would be using 10.0.0.0/26 for the VPC
- Subnet A
CIDR: 10.0.0.0/28
IP Range: 10.0.0.0 to 10.0.0.15
Usable IPs: 11
- Subnet B
CIDR: 10.0.0.16/28
IP Range: 10.0.0.16 to 10.0.0.31
Usable IPs: 11
- Subnet C
CIDR: 10.0.0.32/28
IP Range: 10.0.0.32 to 10.0.0.47
Usable IPs: 11
  
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
- Make the route table public by editing its routes and giving it acces to the internet via the internet gateway
![Configuring the route table for internet access](/images/7.configuring-route-table.png)

- Create the Private Route table in thesame way we created the public route table but without attaching an internet gateway to its route. i.e we would not be editing its route to use the internet gateway, however, we would be configuring that NAT Gateway subsequently to give it an outbound access to the internet. This is very important for security reasons.

## Public and Private subnets with NAT gateway

Public subnet setup (Proper setup public subnet for resource accessible from the internet)

- The public subnet requires a route table with an internet gate way
- Click on the public route table created earlier and click on edit subnet assossiation tab
- Check the two public subnets to assossiate them with the public route table and save the changes

Private subnet setup (Successful creation of private subnet for resources with no direct internet access)

- For the private subnet, click on the private route table created earlier and click on edit subnet assossiation tab
- Select the private subnet and save

Nat Gateway configuration (Correct configuration of a NAT Gateway for private subnet internet access)

- At this point the instances in the private subnet has no way of communication with the internet since its route table doesn't have an internet gateway. Hence we would need to attach a NAT Gateway to it. Nat gateway allows for instances in a private subnet to a controlled access the internet

- Click on the Nat gateway tab on the left panel under VPC services
![Nat gateway tab](/images/nat-gatway-tab.png)
- Click on create nat gateway button
![Create nat gateway](/images/Create-nat-gateway.png)
- Give your nat gateway a descriptive name
- Select one of the public subnet to create the nat gateway
- Connectivity type should be public
- Allocate an Elastic IP address
- Click on the Create Nat Gateway button and it will be successfully created

## AWS MYSQL RDS Setup

RDS instance creation (Proper creation of Amazon RDS instance with MYSQL engine )

Security group configuration

- at this juncture, we should configure all the other secuirty groups we would be needing for this project
- The list of the other security group would aside the public and private subnet security Group would include
- Application Load Balancer Security Group(ALBSG)
- SSH Security Group(SSHSG)
- Webserver Security Group (WSSG)
- Database Security Group (DBSG)
- EFS Security Group (EFSSG)
- We would configure a security group for the AWS Mysql RDS database
- Locate the security group tab under the EC2 services
- Click on create security group
![Creating secuirty group for database](/images/create-secuirty-group-for-database.png)
- Kindly note that all the other security would follow thesame pattern with the inbound rule specifying the type of traffic and the source to allow
- Having completed all the secuirty group setup, lets now setup the RDS database
- Locate the RDS service from the services search bar
- Locate the create database button and click on it
![Find the create database button](/images/create-data-base.png)
- Choose the standard create option
- Choose engine option as MYSQL
- Choose Edition as community edition
- Choose the version you want to work(The lates version preferably)
- Choose the free tier template for this project
- Give the database a descriptive identifier(name)
- Set your prefered master username
- Provide a master password
- Reconfirm the password to make sure they match
- Under the instance configuration, make sure you select the db.t3.micro
- Select your vpc
- Select availability zone
- You can leave other configurations as default
- Click on the the create database button to create the database
- NB: It might take some minutes before the database is provisoned and become active
- After the database becomes active, you have successfully created the database

## Wordpress RDS connection

- Let's setup our ec2 instance to host our wordpress website
- Search EC2 servcie from the search bar
- Click the launch instance button
- Give the web server a descriptive name
- Choose your VPC
- Choose the amazon-linux machine image
- Choose the private subnet we created earlier to shield the server from direct access to public internet
- Disable auto-assign public IP as this would not be need for security reasons
- Choose the exisiting keypair we used for the bastion host previously
- Select the webservers security we already created
- Keep all the other settings default for this project
- Click on the create instance button to create and lunch the instance
![Create and lunch instance](/images/create-and-lunch-instance.png)
- We would now proceed to  install wordpress and other utilities that would hook up with database on the server
- Since our server is in a private subnet and its security group only allows SSH access from the bastion host
- Lunch the bastion host and SSH into it using SSH Client configuration
- Once inside the Bastion we can now SSH into the webserver
- Remember, we already configure the private route-table to use a Nat Gateway. This will enable our instance to have access to the internet so we can install our wordpress and other utilities needed for connection to our database
- Run the following commands

```bash
    sudo yum update -y
    sudo yum install -y httpd php php-mysqlnd
    wget <https://wordpress.org/latest.tar.gz>
    tar -xzvf latest.tar.gz
    sudo mv wordpress/* /var/www/html/
    sudo chown -R apache:apache /var/www/html
    sudo chmod -R 755 /var/www/html
    sudo systemctl enable httpd
    sudo systemctl start httpd

  ```

- NB: You can put these commands in a script for effciency
- The wordpress website has been successfully installed and its now running on our server

Now let's connect the webserver to the database we already created

- Create wp-config.php file in the root of the wordpress folder
- Copy the following configuration into it

  ```php
    define('DB_NAME', 'yourdbname');
    define('DB_USER', 'yourdbuser');
    define('DB_PASSWORD', 'yourdbpassword');
    define('DB_HOST', 'yourdbendpoint');

  ```

Successful connection of wordpress to RDS database

- Replace your database configuration as required with your database details
- You can restart your web server to effect the new changes
- Now the wordpress site have been successfully connected to the database

## EFS connection for wordpress files

EFS file system creation (Proper creaton of an EFS file system)

- Search for the EFS servcie from the services search bar in your AWS console
- Click on create file system
- Choose the VPC where the webserver is located, this ensures that our EFS resides in thesame network
- Choose the availability zones where the web server is located to reduce latency
- Choose general purpose as performance mode. this is suitable for web applications
- Throughput mode should be set to Bursting unless you have specific performance need
- Enable encryption for data-at-rest security
- Click create to finish the setup
- Now the EFS is created successfully
Mounting EFS (Successful mounting of the EFS file system on a wordpress instance)
- To successfully mount the EFS file system, we would need to do the following
- Rememeber we already setup a security group for our EFS which allows NFS access from only the web server secuirty group
Mount EFS on the web server
- Install NFS utilities by running `sudo yum install -y amazon-efs-utils`
- Create a mount directory by running `sudo mkdir -p /var/www/html/wp-content`
- Also install botocore package which is required to resolve DNS names and communicate with AWS services via the EFS mount helper
- To install botocore using pip run `sudo yum install -y python3-pip` if not already installed
- Then run  `sudo pip3 install botocore` to install botocore
sudo pip3 install botocore
`
- Mount the EFS file system by running `sudo mount -t efs fs-xxxxxxx:/ /var/www/html/wp-content`
- repalce fs-xxxxxxx: with your EFS file system ID
- use df -h to verify that to confirm EFS file system is mounted correctly
- Also configure AWS credentials if not done already as this might not allow you EFS file system to mount
- Run `aws configure`
- Then fill in your AWS access key and Secret key
- Provide your default region
- You can also provide your default output format
- Then the configuration is complete

## Wordpress configuration-Correct wordpress configuation to use the shared flie system

- Ensure the wp-content directory has proper permissions to allow WordPress to write files
- Run `sudo chown -R apache:apache /var/www/html/wp-content`

## Application load balancer and auto scaling

For us to see what's happening with our private servers, lets create our load balancer and auto scaling group
as this will help us to access the webservers and their content

ALB creation (Successful creation of application load balancer)

- Navigate to the load balancer section in the EC2 dashboard
- Locate the load balancer create button and click on it
![Locate the load balancer menu](/images/load-balacer-menu.png)
- Choose application load-balancer and click the create button
![Choose application load balancer and click create](/images/Choose-application-load-balancer.png)
- Give your load balancer a descriptive name(e.g BootFastprojectLB)
- Select scheme as internet facing
- Select ipv4 for load balancer ip address type
![Internet facing ](/images/intermet-facing.png)
- Select your VPC
- Select your availability zones(You need at least two diffrent ones in public subnets for better availability and low downtime)
- Select the Application load balancer secuirty group we created earlier
- Let's create a target group for our application load balancer
- Click on the create target group tab
- Give the target group a descriptive name
- Choose the target type as instances
- Choose IP address type as ipv4
- Choose protocol version HTTP
- Leave other settings as default and click
- Select the servers to be included in the target group
- Click include as pending below
![Create target group](/images/create-target-group.png)
- Click on create target group to create the target group
- Listener rule configuration (Proper configuration of listener rule for routing traffic to instances)
- Intergartion with auti scaling
- Go back to the load balancer tab and select the target group we created
- Leave other settings as default
- Click on the create load balancer button
- It might take a couple of minutes to get provisioned
- Now you have successfully created the application load balancer
- You need to copy the dns of application load balancer and paste in your browser to see how the load balancer diirect traffic to the application server

## Steps to Create an Auto Scaling Group

- Navigate to the EC2 Dashboard.On the left sidebar, click Launch Templates.
- Click Create Launch Template.
- Fill out the required fields:
- Template Name: Provide a meaningful name (e.g., web-server-template).
- AMI ID: Choose an Amazon Machine Image (AMI) suitable for your application.
- Instance Type: Select an instance type (e.g., t2.micro).
- Key Pair: Select a key pair for SSH access.
- Security Groups: Attach a security group allowing traffic for your application.
- IAM Instance Profile: Attach an IAM role if needed.
- Add user data for initialization scripts if required(UserData)

``` bash
  
yum update -y
sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd
sudo systemctl start httpd
sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip,mysqli} -y
sudo rpm -Uvh <https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm>
sudo rpm --import <https://repo.mysql.com/RPM-GPG-KEY-mysql-2022>
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld
echo "fs-07d0a7f8f87044530.efs.eu-west-2.amazonaws.com:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a
chown apache:apache -R /var/www/html
sudo service httpd restart

  ```

- Review and click Create Launch Template.

## Configure the Auto Scaling Group

- Navigate to the Auto Scaling Groups section in the EC2 Dashboard.
- Click Create Auto Scaling Group.
- Fill in the details:
- Name: Provide a meaningful name (e.g., web-server-asg).
- Launch Template: Select the launch template created earlier.
- Click Next to configure your ASG.

- Click Attach the Load Balancer In the Load Balancer section:
- Select the appropriate target group associated with your Load Balancer.
- Ensure health checks are configured correctly.
- Click Next.
- Configure Instance Scaling Policies
Desired Capacity: Specify the number of instances you want initially.
Minimum Capacity: Set the minimum number of instances (e.g., 1).
Maximum Capacity: Define the maximum number of instances (e.g., 5).
Optional: Add scaling policies to automatically increase or decrease instances based on CPU utilization, memory usage, or other metrics.
Step 5: Add Notifications (Optional)
Configure notifications to receive alerts on scaling activities.

- Review and Create the Auto Scaling Group
- Review all the configurations.
- Click Create Auto Scaling Group to finalize

## Verification

- Navigate to the Auto Scaling Groups section in the EC2 Dashboard.
- Verify that your ASG is active and associated instances are running.
- Check the Load Balancer to confirm instances are registered and healthy.

Additional Notes

- Ensure proper tagging for your ASG, instances, and related resources to maintain organization and facilitate cost allocation.
- Test scaling policies under load conditions to validate behavior.
- Regularly monitor ASG activities using AWS CloudWatch.
