# Creating Networking Resources in an Amazon Virtual Private Cloud (VPC)

## Objectives
In this lab, we will:

Summarize the customer scenario
Create a VPC, Internet Gateway, Route Table, Security Group, Network Access List, and EC2 instance to create a routable network within the VPC
Familiarize yourself with the console
Develop a solution to the customers issue found within this lab.
The lab is complete once you can successfully utilize the command ping outside the VPC.

Scenario
Your role is a Cloud Support Engineer at Amazon Web Services (AWS). During your shift, a customer from a startup company requests assistance regarding a networking issue within their AWS infrastructure. The email and an attachment of their architecture is below.

Email from the customer
Hello Cloud Support!

I previously reached out to you regarding help setting up my VPC. I thought I knew how to attach all the resources to make an internet connection, but I cannot even ping outside the VPC. All I need to do is ping! Can you please help me set up my VPC to where it has network connectivity and can ping? The architecture is below. Thanks!

Brock, startup owner

A picture of the customer VPC architecture which consists of a VPC with a CIDR range of 192.168.0.0/18, an Internet Gateway, public subnet with a CIDR range of 192.168.1.0/26, and a security group which is around an EC2 instance
## Customer diagram
![image](https://github.com/GowriAyyanar/Hands-on-Labs/assets/152156151/81a139b1-18c6-4b66-bffe-be9dd4f1d1f7)
									
Customer VPC architecture

# Task 1: Investigate the customer's needs

For task 1, you will investigate the customer's request and build a VPC that has network connectivity. You will complete this lab when you can successfully ping from your EC2 instance to the internet showing that the VPC has network connectivity.

In the scenario, Brock, the customer requesting assistance, has requested help in creating resources for his VPC to be routable to the internet. Keep the VPC CIDR at 192.168.0.0/18 and public subnet CIDR of 192.168.1.0/26.

<!-- Creating VPC- Create a VPC - Select VPC only- Name tag(test vpc) - Given CIDR 192.168.0.0/18 - create vpc
Create Subnets -select create subnet- vpc id (test vpc)- Name of subnet is - CIDR 192.168.1.0/28 - create subnet
Create Route table - select route table - create route table - Name (public route table)- select test vpc - create route table 
Create Internet Gateway and attach Internet Gateway - create Internet Gateway- name IGW test VPC - Create - select created IGW test VPC - Attach VPC under the Action - select test vpc - now its attach internet gateway
Add route to route table and associate subnet to route table - Go to route table - select create route table(public route table) - Select routes - Edit routes - Add route - 0.0.0.0/0 target via internet gateway - IGW under test vpc - save changes  -Select Subnet associations - select subnet - save (now we associate subnet to route table)
Creating a Network ACL - Optional
Creating a Security Group -Select Create security group - Nametag - Public security group - Description (allow public access) - select test vpc - inbounds rules allow SSH, HTTP,HTTPS IPV$ select
Launch Instances and Use SSH connect to the Amazon EC2 linux instances -->

Before we start, let's review VPC and its components to make it network compatible.

+ **A Virtual Private Gateway (VPC)** is like a data center but in the cloud. Its logically isolated from other virtual networks from which you can spin up and launch your AWS resources within minutes.
+ **Private Internet Protocol (IP)** addresses are how resources within the VPC communicate with each other. An instance needs a public IP address for it to communicate outside the VPC. The VPC will need networking resources such as an Internet Gateway (IGW) and a route table in order for the instance to reach the internet.
- **An Internet Gateway (IGW)** is what makes it possible for the VPC to have internet connectivity. It has two jobs: perform network address translation (NAT) and be the target to route traffic to the internet for the VPC. An IGW's route on a route table is always 0.0.0.0/0.
+ **A subnet** is a range of IP addresses within your VPC.
+ **A route table** contains routes for your subnet and directs traffic using the rules defined within the route table. You associate the route table to a subnet. If an IGW was on a route table, the destination would be 0.0.0.0/0 and the target would be IGW.
+ **Security groups and Network Access Control Lists (NACLs)** work as the firewall within your VPC. Security groups work at the instance level and are stateful, which means they block everything by default. NACLs work at the subnet level and are stateless, which means they do not block everything by default.

## Creating the VPC
Start at the top of the left navigation pane at Your VPCs and work your way down. Select Your VPCs, navigate to the top right corner, and select Create VPC.

![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/0f75e4f8-95cb-4a73-bfe5-0bf4e96f5cd1)

Name the VPC: Test VPC
IPv4 CIDR block: 192.168.0.0/18
Leave everything else as default, and select Create VPC.

## Creating Subnet
Now that the VPC is complete, look at the left navigation pane and select Subnets. In the top right corner, select Create subnet.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/1e656fd8-8daf-470d-baa0-16b11378c0ae)

![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/ae0239aa-d961-499a-852f-922fc3503b0d)

## Creating Route table
Navigate to the left navigation pane, and select Route Tables. In the top right corner select Create route table.

![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/f7f7795b-c16b-4ed4-bd71-7adff722a6b5)
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/a1e00195-b694-4b0d-ac5b-3e7d602d747f)

## Create Internet Gateway and attach Internet Gateway
From the left navigation pane, select Internet Gateways. Create an Internet Gateway (IGW) by selecting Create internet gateway at the top right corner

![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/84fdad6c-ebff-4677-908a-d4a904dd1b1b)
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/52dc64b2-7713-435b-8178-1eae37a284f3)

Once created, attach the Internet Gateway to the VPC by selecting Actions at the top right corner and clicking Attach to VPC.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/2460c1a9-eaa0-4130-a90e-6bccc94081c6)

Add route to route table and associate subnet to route table
Navigate to the Route Table section on the left navigation pane. Select Public Route Table, and the scroll to the bottom and select the Routes tab. Select the Edit routes button located in the routes box.

On the Edit routes page, the first IP address is the local route and cannot be changed.

Select Add route.

In the Destination section, type 0.0.0.0/0 in the search box. This is the route to the IGW. You are telling the route table that any traffic that needs internet connection will use 0.0.0.0/0 to reach the IGW so that it can reach the internet.
Click in the Target section and select Internet Gateway since you are targeting any traffic that needs to go to the internet to the IGW. Once you select the IGW, you will see your TEST VPC IGW appear. Select that IGW, navigate to the bottom right, and select Save changes.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/7ec3d16d-ad9c-42fe-a4de-dc67ae5a0c09)
From the Public route table dashboard, select the Subnet associations tab. Select the Edit subnet associations button.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/1a18004c-d36b-4f47-b107-42a2ada9fad6)

## Creating a Network ACL
From the left navigation pane, select Network ACLs. Navigate to the top right corner and select Create network ACL to create a Network Access Control Lists (NACLs).
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/b71d6fcf-3aed-4862-8e9e-7da7fae92763)
## Creating a Security Group
From the left navigation pane, select Security Groups. Navigate to the top right corner and select Create security group to create a security group.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/7e95868a-7ece-4365-8ba9-ed48c72972b3)
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/8b46c7b1-81ac-4413-98e2-c84a78ba6782)
The completed security group is shown below. This indicates that for Inbound rules you are allowing SSH, HTTP, and HTTPS types of traffic, each of which has its own protocols and port range. The source from which this traffic reaches your instance can be originating from anywhere. For Outbound rules, you are allowing all traffic from outside your instance.
![image](https://github.com/GowriAyyanar/Networking-Lab-1/assets/152156151/daaf317e-29f7-4388-92b6-649508188b13)


![Taskk 1](https://github.com/GowriAyyanar/Hands-on-Labs/assets/152156151/1fa6da98-727b-49e8-8a71-028ea94e9ece)


# Task 2: Launch EC2 instance and SSH into instance

Lanching Instances and
Use SSH to connect to an Amazon Linux EC2 instance

![Task 2](https://github.com/GowriAyyanar/Hands-on-Labs/assets/152156151/9ef18ac7-9f47-40e6-a97d-1f4d64a67809)

