# Lesson 1 - Intro

## Should already know..

- Create a log into an AWS account
- Know AWS basics
- Be able to SSH into an EC2 instance
- Building applications using AWS

## Highly Available (HA) Design

- Building a failover (f/o) for every level of a system
- Having a standby system ready and able to take over at any point

### History

#### Previously

- Expensive
- Rare

#### Nowadays

- More readily available/deployrable
- Cloud infrastructures like AWS make it possible with a few clicks

### Does

- Provide quick f/o
- Protect against localized hw failures

### Does not

- Does not protect against software deployment failures
- Does not protect against datacenter failure

## Resilient (Resil) Systems

- More expensive
- More complex to run/maintain

# Lesson 2 - Availability Zones (AZs) and Regions

## Intro to AZs and Regions

- An AWS Region contains multiple AZs
  - These zones allow for fault isolation within a region
  - Provide the simplest way to achieve a significant level of redundancy
- Multiple geographical regions
  - Connected via highspeed network
  - Regions themselves are independent
- Networks in AWS are setup via Virtual Private Clouds (VPCs)
  - Exist in 1 AWS Region

## Cornerstone of AWS

AZs and regions allow you to:
- Build a worldwide system
- Create highly redundant platforms

## AWS Infrastructure

- What level of redundancy should you have?
  - Needs vs Wants
  - Costs
- How will users be affected?
- What is the cost of an environment or use case failing?
- What are the repricussions of the service being down?
- What are the contractual obligations of the business?

## Regions

- Regions are isolated
  - Are connected by a highspeed AWS network
- Personalized
- Citizen data are not stored by other countries
- More than 20 regions
- Terabits/multiple terabits of capacity between regions
- Connected via fiber networks
- Only a few services are multi-region
- Most managed on a per-region basis
- Creating one in one region doesn't get replicated
- Exceptions:
  - Identity and Access Mgmt (IAM)
  - CloudFront - Primary goal is to distribute data globally
  - Amazon's Simple Service Storage (S3) - Every region is displayed at once in the AWS console, but the S3 bucket still lives in a single region
- Services are usually released in some regions at a time, then expanded to others
  - Good to know before adopting a service if business deals with multiple regions
  - The top right of the AWS dashboard will indicate your region
  - Also need to specify a region for the Command (cmd) Line Interface (CLI) or Software Development Kit (SDK)

### Current Region

There are multiple regions to choose from and tags associated with those regions

Can see region elsewhere, but navigated to Amazon Elastic Compute Cloud (EC2) then clicked the region dropdown in top right corner

**US East (Ohio)** `us-east-2`

#### Setting up and launching a basic EC2 instance

- Clicked `Launch Instance`
- Selected the `Amazon Linux 2 AMI (HVM), SSD Volume type - ...` option
- Dialog box popped up requesting we select an existing pair or create a new key pair
  - For Windows AMIs a private key file is required to allow you to log into your instance
  - For Linux AMIs, the private key file allows you to securly SSH into your instance
- Key pair will be added to a set of authorized keys for this instance
- There is documentation on [removing existing key pairs from a public AMI](https://docs.aws.amazon.com/console/ec2/launchinstance/key-pair/remove)
- `private key file` in the form of a `(*.pem file)` will be downloaded upon creation
- Key pair name used for example:
  - `launch-wizard-test-1`
  - Saved file location to: `F:\archive\school\udacity_cloud_arch\keypairs`
- Once Key Pair created, was able to launch instance
- Instructions on how to connect to instance located [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html?icmpid=docs_ec2_console)
- Instances continue to run until terminated by the user
  - **NOTE** - This will burn credits continually until terminated
- Can view all instances running from `View Instances` button
- See screenshot `F:\archive\school\udacity_cloud_arch\screenshots\region_demo_ss1` for more info

## Availability Zones (AZs)

- Each Region is composed of multiple AZs
  - Minimum 2, usually 3
- Physically separate complete datacenters
- Separated by a handful of miles
- Low latency/high bandwidth (bw)

### Multiple AZ usage

- By default you do
- Services like RDS and S3 do already

## Virtual Private Cloud (VPC)

Definition:

- Primarily a network
- Virtual because anything in AWS is virtual
- You control it, hence private
- "Cloud" is redundant

More info:

- Security groups are similar to firewalls but not the same
- Can connect VPCs together, even if they're in diff regions
- Can do it between diff AWS accounts, but be careful
- VPC can have almost no cost
- Usually configured to one VPC per region, but more can be configured
- Can treat them as standalone networks or connect them together to form a network hierarchy

What's in them?

- Anything instance-based is inside
- A lot.

What's not?

- Service-oriented AWS services tend to not run in a VPC
- Any global services or core services
  - S3
  - etc  

### Network Range

- VPCs are similar to IP networks
- Need to provide it a network range 
  - Consecutive set of IP addresses
  - Uses Classless Interdomain Router (CIDR) notation
    - `<IP_address>/<number>`
    - `<first_addr_in_range>/<num_addr_in_range>`
    - `/16` uses the maximum of the second half of the IP address as the end of the network range
      - `10.2.0.0/16` goes from `10.2.0.0` to `10.2.255.255` (65,536 addresses)
      - `10.2.0.0./24` goes from `10.2.0.0` to `10.2.0.255` (255 addresses)
    - Two VPCs cannot **connect** if they contain the same range of addresses, but they can exist in the same region
    - They are not related to AZs, they are region dependent

### Requirements for  VPC

- Need the following:
  - VPC itself
  - Routing Table
  - Subnet
  - Internet Gateway (if want to connect externally)
- Make note of:
  - In the description below: 
    - IPv4 CIDR - `10.0.0.0/16`
  - On the left-hand panel Subnet:
    - Filter by our VPC
    - Under Subnet there is a Route Table
      - `10.0.0.0/16` is first - target is local
      - then `0.0.0.0` is the default route for anything that doesn't match the first rule - target is our internet gateway
    - Can click on the internet gateway
      - Attached to our newly greated VPC
- All of these things connected together makes for a publicly accessible subnet within our VPC

## Networking in AWS

- Similar to standard network configuration, but not same
- When you create a subnet you define which AZ that subnet is attached to
  - Anything you want in that subnet gets created in that AZ
- A route table normally lives on a router
  - Don't need to create, provision or pay for a router in AWS
  - Simply create a route table and attach it to one or more subnet
  - Route table can be shared across subnets in different AZs
- Domain Name System (DNS) and Dynamic Host Configuration Protocol (DHCP) are very similar to non-AWS counterparts
  - Can choose how DNS results from VPC
  - Can also configure DHCP on a per-VPC basis
- Supports IPv4 and IPv6
  - IPv6 is as popular as real world
- Internet Gateway is a scalable AWS component for accessing the internet from a VPC
  - This is how resources in the subnet access the internet
  - If a subnet has an Internet Gateway in its routing table the instances with public IP addresses can send traffic directly to the internet and vice-versa
  - When you want to be able to send traffic, but restrict incoming traffic to a public IP address, you can use a Network Address Translation (NAT) Gateway
    - NAT Gateway provides network address translation to resources in a VPC
    - Lives in a particular AZ
  - Internet Gateway is multi-AZ
  - Mostly want multiple NAT Gateways in case one AZ were to fail
- Security Group is like a *stateful* firewall
  - Can be attached to an EC2 instance, RDS database, or other instance in AWS
    - Stateful implies which ports you accept connections on
    - Once established inbound and outbound connections are allowed
- Network Address Control List (ACL) is a *stateless* firewall
  - If any given packet in a TCP stream isn't allowed, the whole connection will fail
  - Applied to a subnet
- Subnet does not need internet connection at all
- Can have a subnet that only talks to other subnets in VPC
  - Useful for things like databases
- Other option: Virtual Private Network (VPN) or DirectConnect back to an existing office or Datacenter network
  - VPN allows for private connections through AWS that is secured through encryption from the internet
  - DirectConnect allows for a fiber optic connection between business locations through a business agreement and hardware in place with AWS to allow for this connection

### Debugging VPC Networks

- Traditional network allows for packet sniffing
- AWS does not allow for this because of the layers of simulation as well as for security reasons
- **VPC Flow Logs** show you information about network flows but not the traffic itself
  - Also shows source and destination (dest)
- **Traffic Mirroring** allows for packet sniffing but not as simple as putting a packet sniffer in promiscuous mode

### Quiz

Which are ways to connect to the internet from AWS?

- Internet Gateways
- NAT Gateways

Which of the following CIDR networks is within `10.0.0.0/16`

- `10.0.1.0/24`
- `10.0.0.0/24`

### Exercise

#### Video Info

- NAT Gateways ask you for a couple options:
  - Subnet
  - Elastic IP Allocation ID
- Choose the subnet for the VPC you've created
  - This is your public subnet
- The NAT Gateway itself sits in a public subnet and has access to the internet from there
- To pick an Elastic IP Address you can allocate a new one or if you have one on your account you can choose that
- Once ou have your NAT Gateway, look at your Routing Tables and look for one that does not have a `Explicit Subnet Association` already
  - The one that does have a subnet association is already in use
- Select the Route Table ID that does not have a subnet associated with it
  - Click the **Routes** button below and see the only Route is `local` route to the CIDR range of the VPC `10.0.0.0/16`

#### More Info

- Create a subnet so that resources in your subnet will be able to access the internet, but will not be directly accessible from the internet
- Allow for resources to be a layer deeper within our network
- Security uses a term "defense in depth", which promotes multiple layers of defense so that any compromised layers do not expose important assets

#### Instructions

- Use the "Launch VPC Wizard" to create a VPC
- select the "VPC with Single Public Subnet option
- Name VPC "Udacity-NAT-Exercise" and keep the default options
- Create the VPC
- Create a NAT Gateway in the new public subnet created
- Select the Route Table in the new VPC that is *not* associated with the public subnet
- Update it to include a default route of the new NAT Gateway
- Create a new subnet in your new VPC and have that subnet use your modified routing table

#### Debug

- Ran into an issue debugging upon trying to add Route Destination
  - Selected *Add route*
  - Typed in `10.` and it auto completed the following series:
    - `10.0.0.0/32`
    - `10.0.0.0/24`
    - `10.0.0.0/16` - selected
    - `10.0.0.0/8`
    - `10.0.0.0/7`
  - Added a *Target* by typing in "NAT" and it then let me choose from my NAT:
    - `nat-01eb9a007b05fd1f9`
  - Upon clicking *Save routes* it yelled the following:
```
Updating the routes of the route table failed

We could not update this route table's routes at this time. View the action log for more details.We've successfully rolled back your route table routes to the state they were in when you attempted to save the new rules.

    Creating routes               Failed
    Rolling back created routes   Rolled back successfully

API error message

This route table is used by a subnet, and doesn't support route destination which are more specific than VPC local CIDR.
```

- Attempting to select a different series from those listed above
  - `10.0.0.0/32`
- Same issue
- When trying `10.0.0.0/16` it says "The route identified by 10.0.0.0/16 already exists."
- Accepted `10.0.0.0/8`

#### Solution

- key differences:
  - For their local they have `10.1.0.0/16` which is fine, it's just a different starting IP address at `10.1` instead of `10.0`
  - For the new one associated with their NAT Gateway they have `0.0.0.0/0`

##### **NAT Gateway + Routing Table Walkthrough**

- Create a VPC titled "Udacity-NAT-Exercise"
- Navigate to NAT Gateway section and *Create NAT Gateway*
  - Pick the subnet associated with the VPC just created
  - *Allocate Elastic IP Address* unless you have one already 
    - (which I did after multiple attempts of this exercise)
    - These never go away, so be careful on how much you make as they go against your quota
  - Rename the NAT Gateway to whatever you want just for clarity
- Click *Edit route tables*
- Filter by your VPC if it isn't already and select the one with no subnet association
- Click on the *Routes* tab and then *Edit Routes*
- Enter a *Destination* of choice, something that does not overlap with the base condition of your routing table
  - For this example, we used `0.0.0.0/0`
- For the *Target* select *NAT Gateway* and it'll pull open a list of your available NAT Gateway options

##### **Subnet Walkthrough**

- Select *Subnets* from the left panel
  - Click *Create Subnet*
- Name your subnet "Private subnet"
- Choose the newly created VPC
- Leave AZ as *No Preference*
- Enter an IPv4 CIDR block
  - Must fall within the VPC CIDR block and cannot overlap with another subnet in this VPC
  - `10.0.1.0/24` works since the default cases are `10.0.0.0/16` (global - range of all avaiable `..255.255`) and `10.0.0.0/24` (standard IPv4 base allocation - only takes the last 255)
    - This works since we take the next 255 above the base occupied amount
  
##### **Results**

- We've now created a private subnet that will add an additional layer of security to our applications

### Edge Cases

#### Cannots

- Cannot use multicast in a VPC
- Cannot put network cards into "promiscuous" mode to sniff ethernet packets
- Certain restrictions on opening up ports for Simple Mail Transfer Protocol (SMTP)
- Cannot have network scans run against your acocunt without discussion with AWS

Cans

- Can cross VPC connections
- Can cross region connections
- Can cross account connections

Q: Which do you think are possible with a VPC?

A:

- Can create permanant connection to an existing datacenter
- Can connect two VPCs
- Can connect two VPCs in different regions
- Can connect two VPCs in different AWS accounts

Q: How many total AZs does AWS have?

A: There are around 25 regions with an average of 3 AZs per region

### Lesson Recap

