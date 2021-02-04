# Project 1

## Setup

Start by cloning [this GitHub repository](https://github.com/udacity/nd063-c2-design-for-availability-resilience-reliability-replacement-project-starter-template)


Utilizing AWS CloudFormation, can create Virtual Private Clouds (VPCs); which means you can create "infrastructure as code".

## Steps

To start we'll need to setup a CloudFormation.  For this example, a YAML file has been provided already:

- click _Create stack_
- _Template is ready_
- _Upload a template file_
- _Choose file_ and navigate to the YAML file provided from the GitHub repo
- _Next_
- stack name was set to `udacity-proj1`
- Parameters:
  - VPC Name: `primary`
  - VPC CIDR block: `10.1.0.0/16`
  - Public Subnet 1: `10.1.10.0/24` (basically all 256 for ..10.0)
  - Public Subnet 2: `10.1.11.0/24` (basically all 256 for ..11.0)
  - Private Subnet 1: `10.1.20.0/24`
  - Private Subnet 2: `10.1.21.0/24`
- not configuring any stack options, so skip this page
- _Create stack_
- wait for the stackt o build out; refresh status until becomes "CREATE_COMPLETE"
- observe the _Outputs_ tab for the created IDs; these will be used later
- in the _Outputs_ tab you can see the different IDs associated with the various components:
  - ApplicationSecurityGroup
  - DatabaseSecurityGroup
  - PrivateSubnets
  - PublicSubnets
  - VPC

| Key | Value | Description | Export name |
| - | - | - | - |

Contents above listed here

- under the _Resources_ tab you can see the created Logical IDs listed
-  from current standing there are some _Events_ that don't complete, but at first glance they appear to be duplicates of already completed versions of the same components

## Data Durability And Recovery

1. Pick two AWS regions. An active region and a standby region.
    - Virginia (active region) - Primary `us-east-1`
    - Ohio (standby region) - Secondary `us-east-2`
1. Use CloudFormation to create one VPC in each region. Name the VPC in the active region "Primary" and name the VPC in the standby region "Secondary".

## Highly durable RDS Database

1. Create a new RDS Subnet group in the active and standby region using private subnets.
1. Create a new MySQL, multi-AZ database in the active region. The database must:
    - Be a “burstable” instance class.
    - Have only the “UDARR-Database” security group.
    - Have an initial database called “udacity.”
1. Create a read replica database in the standby region. This database has the same requirements as the database in the active region. 

## Availability Estimate

Write a paragraph or two describing the achievable Recovery Time Objective (RTO) and Recovery Point Objective (RPO) for this Multi-AZ, multi-region database in terms of:

1. Minimum RTO for a single AZ outage
1. Minimum RTO for a single region outage
1. Minimum RPO for a single AZ outage
1. Minimum RPO for a single region outage

## Demonstrate normal usage

In the active region:

1. Create an EC2 keypair in the region
1. Launch an Amazon Linux EC2 instance in the active region. Configure the instance to use the VPC's public subnet and security group ("UDARR-Application").
1. SSH to the instance and connect to the "udacity" database in the RDS instance.
1. Verify that you can create a table, insert data, and read data from the database.
1. You have now demonstrated that you can read and write to the primary database

### Debug

- Couldn't connect via command prompt attempting to SSH
  - User replied to my query [here](https://knowledge.udacity.com/questions/413801)
  - Response:

    ```
    Your key should not have access to others. I don't recal how to make it work in windows. In linux you should have 400 permitions (which I think is read only to your user) and the ownership of the file. This doesnt work in NTFS, in other words, not even using WSL or cygwin if you are working in mounted partinion using NTFS. So if you can use WSL then use the ext4 partition or use a docker container. 
    ```

  - Ended up going down the Docker Route
    - More info from [these instructions](https://docs.docker.com/docker-for-windows/install/)
    - More instructions on the WSL installation [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-5---set-wsl-2-as-your-default-version) (only after Docker installed and restart occurs)
  - Making changes to your Dynamic Profile [here](https://docs.microsoft.com/en-us/windows/terminal/dynamic-profiles)
  - To see your settings, you can click the dropdown in the Windows Terminal window
  - To see defaults or configuration information hold Alt + click Settings fromt he dropdown window
  - Following [Docker Tutorial](http://localhost/tutorial/)
- Trying the Amazon WorkSpaces approach
  - Creating the following account:
    - Free tier eligible
    - Bundle: `Standard with Amazon Linux 2`
    - UN: `starlazar4u2`
    - FN: `Chris`
    - LN: `Lazar`
    - email: `starlazar4u2@gmail.com`
  - Get up to 40 hours per month (or first 2 months from launch date)
  - Takes up to 20 min to become avail
  - More info on [using SSH from Workspace](https://aws.amazon.com/premiumsupport/knowledge-center/linux-workspace-ssh/)
  - needed to install [workspace desktop client](https://clients.amazonworkspaces.com/)
    - Invite link was sent to spam, had to fish it out
    - PW: W5!
- Mentor suggested another option: [forum response](https://knowledge.udacity.com/questions/462126)
- Realized I wasn't naming the MySQL DB in the "Initial Database Name"; this is found under the "Additional Configuration" section
- Need to have automatic backups enabled as well

### Creating a Read Replica DB

Steps:

1. Create a DB in the primary region
  1. keep "Standard create"
  1. select "MySQL" for "Engine type"
  1. can use the "Free Tier" option for DB type
  1. in "Settings" can change the "DB instance identifier" if desired
  1. set the "Master password" to something you'll remember for when you SSH into your instance and need to connect to your DB
    1. can optionally set the "Master username"
  1. in "Availability & durability" can select "Do not create a standby instance" if it's not grayed out
  1. in "Connectivity" verify that the "Existing VPC security groups" option has unchecked `default` and has `UDARR-Database` selected
    1. should have the "Subnet group" you've created in the RDS section as well as the VPC you've created as a part of your CloudFormation stack
  1. take not of the "Database port" in that section, usually defaults to `3306`
  1. under "Additional configuration" enter the name for the database you'd like to appear when you SSH into your instance
    1. Leave the remaining components in the "Additional config" section as is
1. once DB is created, select the radio button associated with it
1. choose the "Actions" dropdown
1. select "Create read replica"
1. leave the "Replica source" as whatever it was based on the DB you're using to create your read replica off of
1. name the DB instance identifier (I just incremented my `database-1` count up to `2`)
1. choose the "Region" separate from the current region (as that's part of the assignment)
1. should automatically select the correct "Subnet group" from the region you've chosen as your read replica region
1. select the "UDARR-Database" for the "Existing VPC security groups"


See [AWS Documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ReadRepl.html#USER_ReadRepl.Create)

Also see [video containing demonstration](https://www.youtube.com/watch?v=COsx7UrMGL4&feature=youtu.be)

also also.. [knowledge base question pertaining to this issue](https://knowledge.udacity.com/questions/424268)

### SSH into Instance

[AWS Documentation on SSH into EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

- Needed to change the permissions to the active `user` (in this case it was the name Chris Lazar) in the security options
  - in order to see the users' name, just type 'user' and see what results from the auto-complete
- In order to edit security options, file must be located on the primary drive (C:\\)
- Remove all other permissions to the key-pair file other than `Read` access for the user in question
  - I don't even have admin or system or owner rights on any group on the system; just lazar.christopher@live.com
- The following command succeeded for SSH:
  - `ssh -i C:\dev\udacity_cloud_arch\keypairs\proj1-key-pair.pem ec2-user@ec2-54-89-48-10.compute-1.amazonaws.com`
  - path to key-pair (.pem since it's Windows instead of .ppk for Linux) file `C:\dev\udacity_cloud_arch\keypairs\proj1-key-pair.pem` 
  - `ec2-user` is required since this is an Amazon EC2 instance
  - `ec2-54-89-48-10.compute-1.amazonaws.com` is the DNS path listed on my instance information Networking page
- Once I'm actually able to SEE the Udacity db I created, I should be able to type the following command:
  - `USE udacity` - see [the walkthrough](https://knowledge.udacity.com/questions/262584)

## Website Resiliency

