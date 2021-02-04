# Lesson 3 - Building For Resiliency

## Lesson outline

- AWS Services which are server-based
  - Share common patterns in availability
- DynamoDB
  - Create a multi-region database
- S3
  - AWS object store
  - functionality
  - Bucket to manage lifecycle
- EC2 & Compute Services
  - Can be grouped together to create resilient applications

## Resiliency everywhere

To create something resilient, the more resilient the parts are the more resilient the wholistic portion of the project will be.

- Should build things fault tolerant (all parts)
- Not only the network but the underlying services will be highly available
- AWS makes it possible for all components to be highly available and fault tolerant
  - it's a matter of time and budget constraints as to whether or not it's worth it
  - in the cloud it takes a bit more work to make it happen

## Tradeoffs to consider

- Can build a system to contain all of the following:
  - singular w/ no f/o
  - mutli-AZ
  - multi-region
  - automated lightning fast f/o
- need to determine level of redundancy required

Things to ask yourself:

- How important is it that this product or service stay available?
- Will the company be hurt from it going down?
  - if so how significantly?
- Does it bring in revenue or is the service free?

## Server-based services

- server-based services are referred to as "instances"
- examples:
  - RDS
  - ElastiCache
- the above are instance based that you can run on one instance, but will not have any fault tolerance
- in order to gain high availability you need to tell the service to provision a second instance
- this will provide an additional instance for the primary instance to _failover_ to
- to determine if a service is server/instance based is if the service is a pre-existing product that AWS has create a service with (MongoDB, Redis, MySQL, Postgres)
  - MongoDB - a NoSQL db that stores data in JSON-like documents with flexible schemas
  - Redis - an in-memory data structure that's used as a distributed in-memory key-value database, cache and message broker for complex/abstract data types
  - MySQL - open-source relational database management system ("My" Structured Query Language (SQL))
  - Postgres - same as MySQL but emphasizing in extensibilty and SQL compliance
- in order to get a mutli-AZ availability you need to configure a subnet gropu within the service
  - a subnet is attached to an AZ
  - creating a grouping of subnets within a service tells the service where it can place the primary and standby instances of a service
    - assumedly through a default schema based on the size of the subnet allocated

### How to spot a server-based service

Answering yes to any of the following will indicate:

- Does the service run in your VPC?
- Are you configuring a group of subnets for the service to use?
- Do you provide a security group for the service?
- Do you need to choose an instance size?
- Is the service running a no-AWS developed application?
  - (MongoDB, Redis, MySQL, Postgres)
- Examples
  - ElastiCache
    - A managed caching service
  - ElasticSearch
    - A managed elastic search
  - DocumentDB
    - AWS's MongoDB
  - Elastic Map Reduce
    - AWS's managed big-data service
- the service requires that you select options that indicate you want redundancy

What are the advantages of paying for AWS to run these services?

- AWS has teams of people assigned to maintaining and performing these services
- RDS costs about the same as renting the equivalent-size EC2 instance and running the database yourself
  - this requires system planning and maintenance
  - RDS takes care of all of that for us

## Redundancy in AWS

- Subnet Groups are key to creating multi-AZ redundancy in server-based services
- Subnet Groups define the diff AZs that your service will run in
- Multiple instances = fast f/o
- Multi-region redundancy is a bit trickier
  - May not be possible to run a service with f/o between regions
- Subnet group should include subnets from multple AZs
- Run multiple instances (if one AZ goes down, need to wait for another instance in a diff AZ to be created)
- examples:
  - in ElastiCache you configure a cluster
  - in RDS you choose a multi-AZ configuration
- want to create a "hot standby" that can take over for that failed service in moment's notice
- Sometimes it's as easy as a checkbox
  - "Multi-AZ deployment" \[X] Yes [ ] No.
- RDS has read-only versions of the service in other regions as a backup
- Other services like Elastic Search doesn't allow for multi-region redundancy, but you can migrate backups to other regions in the case of a failure
- Redundancy for a server will be double the cost often and is not cost-effective since it's not "double the potential", it's just a backup

## Quiz 1: Server-Based Services

Which services are "Server based"?

- RDS 
- ElastiCache

Which are not examples of server-based services?

- S3
- Route53

## Exercise: Server-Based Services

First, create an ElastiCache subnet group in the default VPC using each available subnet and then create a multi-AZ redis cluster.

### Looking into subnet groupings

[Reference documentation](https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/SubnetGroups.Creating.html)

- a _cache subnet group_ is a collection of subnets you may want to designate for your cache clusters in a VPC
- when launching a cache cluster in a VPC you need to select a cache subnet group
- ElastiCache will assign IP addresses within that subnet to each cache node in the cluster

### Can be done via CLI or Console

- Console will always be the graphical interface where you can select things manually
- CLI will be command line interfaces for accomplishing the same thing in text

### Creating a Subnet Group (Console)

- Sign in to the AWS console and open the [ElastiCache Console](https://console.aws.amazon.com/elasticache/)
- Choose _Subnet Groups_ from the navigation list
- Choose _Create Subnet Group_
- The CIDR notation `/16` implies how much of your network address is masked
  - therefore, a `/16` would imply that of the 32 bits available, 16 of them are masked
  - 32 - 16 = 16 -> 2^16 = 65536 available addresses
  - a `/28` would imply: 32 - 28 = 4 -> 2^4 = 16 available addresses

## Solution

### Create Subnet Group

1. Go to the ElastiCache service.
1. Select "Subnet group" -> Create Subnet group
1. Name the group "Udacity" and give it a Description
1. Select the default VPC
1. For each Availability Zone, select the associated subnet and then save.

### Create Redis cluster

1. Select Redis -> create
1. Change the "Number of replicas" to 1
1. Make sure "Multi-AZ with Auto-Failover" is checked
1. Note that your newly created subnet group is selected1. by default.
1. Click Create and wait for the cluster to be available.

## DynamoDB

- native AWS service for non-relational databases
  - relational databases would use RDS
- multi-AZ by default
- can be made multi-region
  - requires _DynamoDB Streams_ and _Global DynamoDB table_ created
- can configure a cache layer in front of the DB
- _DynamoDB Streams_ allow every change made to _DynamoDB table_ to be 'streamed' into other services
- 1 DB = 1 table
- 1 primary key, optional sort key
  - can have multiple primary or sort keys on the table if configured
- read/write thru-put is charged by # of operations/second
  - you decide how much per second and include them in the index
  - can also pay on demand or autoscale over time
- drastically simpler
- quite powerful and suitable in many workloads
- can handle 10's of millions of requests a second and scaling as needed
- AWS runs this service among multiple AZs
  - AZ failure does not affect your DynamoDB
- cost of running in multiple AZs is baked into this service
- still most likely cheaper than a DB service like RDS
- pay for what you use vs traditional constant charging from regular db service

### Global Tables

- stream is an ongoing stream of events
- affects all changes made to a table
- when table changes are made, event is placed into the DynamoDB stream for each change
  - creating 
  - updating
  - deleting (all for any item in the table)
- can use a lambda function to compare the new change event to existing data or change events including..
- _Multi-Region_: _Global Tables_
  - _Multi-Region tables_ (multi-region redundancy)
- a _Global Table_ is a _DynamoDB table_ replicated across regions
- in order to make it multi-region:
  - enable _DynamoDB streams_
  - select region in which you'd like to replicate your table
- can write in any table and all other tables in other regions will be updated
- being _Active\Active_ (A/A) allows you to run services in multiple regions and have those apps access the data locally and operate with some autonomy
- mutli-region _A/A_ datastores are a big deal
- many DBs and services can't accomplish this or require a lot of effort
- DynamoDB offers a lot of resiliency

#### Key takeway

- DynamoDB Streams capture all changes made to a table
- actions that require table modification specifically
- not just read-from actions
- utilizes Multi-Region A/A DynamoDB Tables

## Quiz 2: DynamoDB

### Q: DynamoDB is Multi-Region by default

- False - but it's still easy to set up

### Q: Which are true about DynamoDB Streams?

True:

- They stream all changes to a table
  - Creates, Updates, and Deletes

False:

- They can only stream to the same region as the table
  - They can easily be made to be mutli-region
- They only stream inserts
  - Creates, updates, and deletes also
- They are required to backup a DynamoDB table
  - since they are multi-AZ they consistently maintain a fully copy of the DB in other AZs or regions depending on how you're set up

## Exercise: DynamoDB

DynamoDB is not like the other server-based services.  It's 'serverless' in the sense that you do not have control over the physical infrastructure.  You do not pay for the service you when you are not using it (except used storage space).

Redundancy is also handled differently compared to server-based services.  DynamoDB offers multi-region, A/A service if you elect it. This is called DynamoDB Global Tables.

Create a DynamoDB table and extend it as a Global table into a second region.

### DynamoDB workthrough

- created the _Table Name_: `Udacity-Exercise-DynamoDB`
- _Primary Key_ represents the main value in which the table is organized by
  - Examples used:
    - CustomerId
    - GameId
      - This one was not recommended if there are only 1 or 2 popular games; i.e. not enough differentiating _primary_ factors
- made _Primary Key_: `GameId` and left the variable type as _String_
- did not choose to add a _sort key_
- allocated default settings by hitting checkbox:
  - no secondary indexes
  - provision capacity set to 5 reads and 5 writes
  - basic alarms with 80% upper threshold using Amazon's Simple Notification Service (SNS) topic "dynamodb"
  - Encryption at Rest with DEFAULT encryption type
- was notified "You do not have the required role to enable Auto Scaling by default."
- upon completion opens up an overview window with all of the components allocated for the DynamoDB
- at the top there is a tab for _Global Tables_ which will state the following:
  - "To create a global table, ensure that DynamoDB Streams are enabled"
- select _Enable Streams_
  - only 1 view type available as an option: _New and old images_
- click _Add region_
  - this will "add a replica to global table"
  - chose US East (N. Virginia)
  - Global table version is chosen by default
    - default region (**US East**: `Ohio`) uses _Global table version_: `2019.11.21`
    - added replica region (**US East**: `N. Virginia`) uses _Global table version_: `2017.11.29`
- was given the following error:

  ```
  Internal server error (Service: AmazonDynamoDBv2; Status   Code: 500; Error Code: InternalServerError; Request ID:   LAP84OMEVVTMDD0NUDHMPJGU1NVV4KQNSO5AEMVJF66Q9ASUAAJG;   Proxy: null)
  ```

- when trying to do California instead was given the following error:

  ```
  Table write capacity should either be Pay-Per-Request or AutoScaled. (Service: AmazonDynamoDBv2; Status Code: 400; Error Code: ValidationException; Request ID: 6N4LSREMS4JQ0N03J9S8P8L1C7VV4KQNSO5AEMVJF66Q9ASUAAJG; Proxy: null)
  ```

- under the _Capacity_ tab you can choose the _Provisioned (free-tier eligible)_ or _On-demand_ options
  - [this Google search result](https://aws.amazon.com/blogs/aws/amazon-dynamodb-on-demand-no-capacity-planning-and-pay-per-request-pricing/) indicated that the _On-demand_ option would coincide with the "Pay-Per-Request" log mentioned in the second error when choosing California
- selecting the _On-demand_ option links to [this information page](https://aws.amazon.com/dynamodb/pricing/)
- the changes take a moment to update..

## Solution

### Setting up a DynamoDB table

1. Select DynamoDB -> Create table
1. Name the table "udacity" and the primary key "id"
1. Uncheck "Use default settings"
1. Change "Read/write capacity mode" to "On-demand"
1. Click Create

### Configuring the table to be a Global table

1. Select the Global Tables tab
1. Note the notice about needing to enable Streams and click the Enable Streams button
1. Click Add region
1. Select a region and click Create replica and wait for the table to be available

Success message comes up:

```
Select a region and click Create replica and wait for the table to be available.
```

You now have a table that can be read from and written to in multiple regions. In the Items tab, click Create item in the table in one of the two regions, then switch to the other region and observe that you can see the item there as well.

## Simple Storage Service (S3)

- [S3](https://aws.amazon.com/s3/) is one of the very first AWS services and is an object store
- create buckets and store unlimited number of objects in a bucket (up to exabytes of data)
- many diff levels of storage options within S3
- options vary between cost of use and time or freq to retrieve objects stored
- AWS has features to execute "Lambda" functions on the creation, updating, or deletion of objects
- can also create a lifecycle policy for the duration items have been in a bucket
  - can move or delete based on these policies
- objects are at the very simplest form just a file
- pay-as-you-go
  - the most you pay $0.03/gigabyte per month
- highly durable
  - likelihood you'll lose something
  - not to be confused with availability
  - provide 99.99999999999% durability of objects
  - an object list of 10,000,000 will lost 1 item every 10,000 years
- create copies of buckets
- same region replication
- cross region replication

### Storage Classes

Storage class options:

- Standard
- Infrequently Accessed (IA)
- Intellgient Tiering
- Glacier
- Glacier Deep Archive

Elaboration:

- IA data is accessed the same way, but charged less for storage and are charged more for accessing it
- if you're unsure how often, you can use Intelligent Tiering to let the software determine what data should be IA vs standard
- Glacier is colder storage, storage not as immediately accessible
  - backups or less-often needed data
- Glacier Deep Archive is for even deeper storage
  - rarely or nearly-never needed to be accessed
  - price goes down even further for Glacier and Glacier deep

### S3 Features

- usage patterns tend to change over time
- data today will be accessed a lot today, less next week, even less next month, next year almost not at all
- S3 _lifecycle policies_ allow you to do this
  - you define the bucket and for what duration
  - have it stay standard for 3 months
  - then migrate to IA after that
  - then move to Glacier after a year
  - can be done for certain objects, types of objects, or certain sub-sections within a bucket
- can also delete object after a certain amount of time
- consider data storage costs over time, this is important for the industry
- S3 events allow you to take action when things happen in the S3 buckets
- S3 events allow you to run a _Lambda_ function on the activity of objects in your S3 bucket
  - can run a _Lambda_ on the creation of any object
  - say a photo gets added to a bucket
    - _Lambda_ event could take the photo and make multiple resolutions of that photo
  - decoupling file uploading from file processing
- can turn on versioning for an S3 bucket
  - if you upload an object, modify it, then modify it again; S3 will maintain 3 copies of that object (version-controlled)
  - to your application it'll only look like 1 object with your most recent change; however if you dig into the details of that object you'll see that S3 is holding on to all copies of that object
  - with versioning turned on, you can delete an object and it'll disappear from your bucket, but it will not be gone
    - S3 uses what we call _delete markers_ which will allow you to recover deleted objects
  - allows you to roll back objects even after deletion
  - can go back and see what the original version of the object ever looked like
  - ransomeware attack for example - you could just go back to an old version and be done with it

  ## Quiz 3: S3

  ### Q: You can store a maximum of 1 trillion objects in an S3 bucket

  A: False - nearly infinite

  ### Q: Which can you do with an S3 lifecycle policy?

  - Move an object to a different storage class
  - Delete an object

  What can't you do?

  - Move an object to DynamoDB
  - Compress the object

## Exercise: S3

- S3 has evolved to offer:
  - a variety of classes of storage
  - access options
  - diff types of events
- value of data diminishes over time
- over time, may want to save money and spend less on durable old data
- may even want to delete old data
- can utilize S3 _Lifecycle_ rules to do this

### Objective

Setup and S3 bucket with a Lifecycle rule to delete any object placed in the bucket after one day. In actual use, you can select the amount of time that data is valuable before deletion.

- name the S3 bucket: `udacity-s3-exercise`
- name the rule: `udactiy`
- apply the rule to the entire bucket
- _Transitions_ this is for transitioning one storage class to another
- _Expiration_ is for expiring or deleting objects - not dealing with versioning for this exercise

## Solution

- Navigate to the S3 page
- Create a new bucket
- Under _Management_ tab, select _Create Lifecycle Rule_
- can choose to make it apply to all objects or just certain objects
- select "Expire _current_ versions of objects"
- new dialog box appears where you can select number of daya until expired

## Computer Services

- AWS offers
  - EC2
  - Lambda
  - various container services
  - etc
- similar to server-based services because (b/c) they are not Multi-AZ by deafult
- EC2 can be run as a group with an [AutoScaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html)
  - _Auto Scaling Group_ contains a collection of Amazon EC2 instances
  - treated as logical grouping for the purposes of automatic scaling and management (mgmt)
  - enables features like health check replacements and scaling policices
- _AWS Lambda_ has diff connectivity modes
  - if it doesn't need to access your VPC then it can run independently
  - can be configured to access your VPC and allow the Lambda to connect to diff AZs (subnets)
- individual instances or containers are generic servers
  - just like a physical servers they are susceptible to hardware failure
  - if an AZ goes down, any server in that AZ also goes down
- to make multi-AZ, need to have it automatically create a new server and have it start working unattended
  - can be done from a custom image and building from it
  - CloudFormation and Teraform can do these things for you
  - can configure an AutoScaling group
  - can maintain a number of instances
  - can be spread across multiple AZs
- if instances you've created support requests, can put them behind a _load balancer_
  - able to pass requests to computing resources in multiple AZs
  - _Load Balancer_ itself is multi-AZ, cannot span regions
  - each instance has a diff IP address
  - can be seen using a DNS lookup on the _Load Balancer_
  - IP address can change over time for a _Load Balancer_
  - can load balance http traffic with an _Application Load Balancer_
    - will spread out HTTP requests
    - allow you to terminate SSL
  - non-HTTP traffic can make use of a Network Load Balancer
    - operates at TCP layer
    - more direct control over traffic being distributed
- Lambda is serverless computing
  - A Lambda runs software
  - you just need to worry about the software
  - Lambda will do the server portion
  - if you're Lambda function doesn't need to access anything in your VPC the Lambda function can run redundantly without any additional measures
  - will continue to run even if an AZ fails
  - if you do need it in a VPC, then you would need to configure the Lambda like you would a server-based service
  - need to specify VPC to which it can connect and which subnets to use
  - will automatically spread the individual invocations across the AZs

## Edge Cases

- might be cheaper to run a _Lambda_ service manually/separately in multiple regions instead of trying to configure a multi-Region service or multi-Region _Lambda_ function
- once your familar with a wide array of AWS services, you can use a multi-AZ or multi-region service to fit your needs
- "think left, think right, think low, think high, oh the things you can think of if only you try" - Dr. Seuss

### Q: Which of the following are indicators that you’re using a “server based” service?

- the service requires you to define a Subnet Group
- the service runs in your VPC
- the service requires you select options that you want redundancy

False answers:

- the service is a Global service

### Q: Which of these services are the least expensive to use for services that run randomly, but infrequently everyday (pick 3)?

- [X] Lambda
- [X] S3
- [X] DynamoDB
- [ ] RDS
- [ ] EC2

## Recap

- Server-based Services
- DynamoDB
- S3
- Compute Services

Saw a variety of AWS data storage and compute options. Things to consider:

- use cases
- usage patterns
- cost restraints
- availability requirements

Lesson Objectives:

- take advantage of diff HA options on AWS
- create multi-AZ services
- create multi-Region services
- identify what availability options exist for which AWS services
- take advantage of resil features in AWS services
