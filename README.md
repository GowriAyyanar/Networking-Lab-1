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
Creating a Security Group -Select Create security group - Nametag - Public security group - Description (allow public access) - select test vpc - inbounds rules allow SSH, HTTP,HTTPS IPV$ select  -->

Before we start, let's review VPC and its components to make it network compatible.

+ **A Virtual Private Gateway (VPC)** is like a data center but in the cloud. Its logically isolated from other virtual networks from which you can spin up and launch your AWS resources within minutes.
+ **Private Internet Protocol (IP)** addresses are how resources within the VPC communicate with each other. An instance needs a public IP address for it to communicate outside the VPC. The VPC will need networking resources such as an Internet Gateway (IGW) and a route table in order for the instance to reach the internet.
- **An Internet Gateway (IGW)** is what makes it possible for the VPC to have internet connectivity. It has two jobs: perform network address translation (NAT) and be the target to route traffic to the internet for the VPC. An IGW's route on a route table is always 0.0.0.0/0.
+ **A subnet** is a range of IP addresses within your VPC.
+ **A route table** contains routes for your subnet and directs traffic using the rules defined within the route table. You associate the route table to a subnet. If an IGW was on a route table, the destination would be 0.0.0.0/0 and the target would be IGW.
+ **Security groups and Network Access Control Lists (NACLs)** work as the firewall within your VPC. Security groups work at the instance level and are stateful, which means they block everything by default. NACLs work at the subnet level and are stateless, which means they do not block everything by default.

![Taskk 1](https://github.com/GowriAyyanar/Hands-on-Labs/assets/152156151/1fa6da98-727b-49e8-8a71-028ea94e9ece)


# Task 2: Launch EC2 instance and SSH into instance

Lanching Instances and
Use SSH to connect to an Amazon Linux EC2 instance

![Task 2](https://github.com/GowriAyyanar/Hands-on-Labs/assets/152156151/9ef18ac7-9f47-40e6-a97d-1f4d64a67809)

