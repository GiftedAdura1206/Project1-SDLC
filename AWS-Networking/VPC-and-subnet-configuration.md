# What is AWS VPC

An Amazon Virtual Private Cloud (VPC) is like your own private section of the Amazon cloud, where you can place and manage your resources (like servers or databases). You control who and what can go in and out, just like a gated community.

#### The essential steps to creating a VPC and configuring core network services:
-   The Default VPC
-   Creating a new VPC
-   Creating and configuring subnets

### The Default VPC
The Default VPC is a pre-configured networking environment in AWS that allows you to deploy resources immediately. It includes default security and network settings, which can be customized as needed.

![alt text](<images/image 1.png>)

A Default VPC, provided by AWS in each region (like a pre-built house in a city), comes with default settings that let you start deploying applications immediately. These settings are customizable, allowing you to tailor the environment to your specific needs.

### Creating a new VPC
As we want to learn step by step and observe the components, choose the "VPC only" option, we'll use the "VPC and more" option later. Enter "TGCD1-vpc" as the Name tag and "10.0.0.0/16" as the IPv4 CIDR. The "10.0.0.0/16" will be the primary IPv4 block and you can add a secondary IPv4 block e.g., "10.64.0.0/16". The use case of secondary CIDR block could be because you're running out of IPs and need to add additional block, or there's a VPC with overlapping CIDR which you need to peer or connect.

![alt text](<images/image 2.png>)

Leave the tags as default (you can add more if needed), then click `Create VPC`.

Once the VPC is created, it is assigned a VPC ID, and a main route table is automatically generated for it.

![alt text](<images/image 3.png>)

Now that a VPC has been created, we need to configure the subnets.

### Creating and configuring subnets

Subnets are smaller segments within a VPC that help organize and manage network resources more efficiently. Think of them as dividing an office building into departments, each subnet represents a section with a specific purpose or set of resources.

![alt text](<images/image 4.png>)

Go to VPC > Subnets > Create Subnets and select the VPC that you've created previously - the **TGCD1-vpc** or anything you tagged your VPC

click on **CREATE SUBNET**

![alt text](<images/image 5.png>)

Enter the subnet settings details, but do not click "Create subnet" yet. Instead, click "Add new subnet" to add the remaining subnets (4 in total). Once all required subnets have been added, click "Create subnet" to proceed.
Note: If you don’t select an Availability Zone, AWS will assign one at random.

![alt text](<images/image 6.png>)

### Understanding Public and Private Subnets in AWS VPC

In AWS VPC, subnets are like rooms within a secured building (the VPC). Some rooms have windows and doors that open directly to the outside. These are public subnets with internet access. Others are internal rooms with no direct external access. These are private subnets, used for sensitive operations that don’t require exposure to the internet.

Public subnets are great for resources that need to connect to the internet, like web servers. Private subnets are great for resources that you don't want to expose to the internet, like databases.

Understanding public and private subnets helps you to organise and protect your AWS resources better. Always remember, use public subnets for resources that need internet access and private subnets for resources that you want to keep private.

### Introduction to Internet Gateway and Routing Table

An Internet Gateway in AWS is like the main entrance of a secured building (your VPC) that allows communication with the outside world. Without this entrance, no one can enter or leave, keeping all internal activities isolated from the internet. 

In a building (your VPC), the Internet Gateway is like the main entrance that lets people (data) come and go. The Route Table is like a directory or floor plan that tells people which hallway or elevator to take to reach the correct room (subnet or resource). Without the entrance, no one can access the building, and without the directory, they wouldn’t know where to go inside.

### Public Subnets
Technically, the subnets are still private. You'll need these to make it work as public subnets:

-   An Internet Gateway (IGW) attached to the VPC
-   Route table with default route towards the IGW
-   Public IP assigned to the AWS resources (e.g., EC2 instances)

Go to VPC > Internet gateways and click **Create internet gateway**

![alt text](<images/image 7.png>)

Attach the IGW to the TGCD1-vpc

![alt text](<images/image 8.png>)

select the VPC

![alt text](<images/image 9.png>)

To maintain the privacy of the private subnets, we ensure they do not have a default route to the internet. To achieve this, we create a separate route table specifically for the public subnets, which will contain the route to the Internet Gateway. This separation keeps private subnets isolated while allowing public subnets controlled internet access.

## What is a Routing Table?
A Routing Table acts as a guide, directing data within a Virtual Private Cloud (VPC) to its destination. For instance, if data needs to reach the internet, the Routing Table specifies the path through the Internet Gateway.

### Creating and Configuring Routing Tables
Now that we have our entrance and exit (Internet Gateway), we need to give directions to our resources. This is done through a Routing Table. It's like a map, guiding your resources on how to get in and out of your VPC.

Let's go to the route table menu and create a route table for the public subnets.

Put a name for the route table e.g., tgcd-vpc-public-rtb and select the desired vpc - "TGCD1-vpc"

![alt text](<images/image 10.png>)

Once created, edit the route table, add a default route to the Internet Gateway (IGW)

![alt text](<images/image 11.png>)

![alt text](<images/image 12.png>)

![alt text](<images/image 13.png>)

Next, go to the "Subnet associations" tab and click **Edit subnet associations**

![alt text](<images/image 14.png>)

Select the public subnets and click **Save associations**

![alt text](<images/image 15.png>)

Now that the VPC is ready, you can run an EC2 instance in public subnets if they need Internet access or in private subnets if they don't.

**tgcd-vpc-public-rtb**: A route table with a target to Internet gateway is a public route table.

**tgcd-vpc-private-rtb**: A route table with a target to NAT gateway is a private route table.

### Private Subnets and NAT Gateway

In an AWS VPC, private subnets isolate resources, like databases, from direct internet access. A NAT Gateway enables these resources to securely access the internet for updates or downloads without exposure.

A NAT Gateway in an AWS VPC enables resources in a private subnet to securely initiate outbound internet connections for updates or downloads, while blocking inbound access from external services, including databases, ensuring one-way traffic.

### Creating a NAT Gateway and Linking It to a Private Subnet
Go to VPC > NAT Gateways and click "Create NAT Gateway"

![alt text](<images/image 16.png>)

Create the NAT Gateway named "tgcd-nat" under one of the private subnets which I choose the tgcd-private1a as the subnet.

![alt text](<images/image 17.png>)

Let's go to the route table menu and edit the private route table we created earlier, add a default route to the Network Address Translation (NAT) Gateway

![alt text](<images/image 18.png>)

Choose tgcd-vpc-private-rtb, select Edit routes, and select Add route. Under the Target, select the NAT gateway named "tgcd-nat"

![alt text](<images/image 19.png>)

Next, go to the "Subnet associations" tab and click "Edit subnet associations"

![alt text](<images/image 20.png>)

![alt text](<images/image 21.png>)
