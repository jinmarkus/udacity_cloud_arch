# Lesson 4 - Business Objectives

## Lesson outline

- Uptime
- Downtime
- Recovery Time Objective (RTO)
- Recovery Point Objective (RPO)
- Disaster Recovery

### Lesson objectives

- be able to:
  - calculate avail in terms of up & down time
  - set reasonable business metric for RTO and RPO
  - make determinations on what types of DR plans a company neeeds
  - implement a DR plan
- should understand the metrics your compnay is committing to and use this understanding to inform business decisions

Recovery Time Objective:

- committment to the amount of time your service is down

Recovery Point Objective:

- max amount of data lost over the time for which your system is down

Disaster recovery:

-  extreme of avail and resil - bringing up your sys in another place (typically region)
- normamlly done in a region outage or other disaster

## Business objectives

Business objectives are where the other business functions of your company meet with the Engineering function.  These other areas of the company focus on signing customers, managing finances, supporting customer, advertising your products, etc.  Where all of these teams meet is in the realm of contracts, commitments and uptime.  This is where other parts of a business have to collaborate with Engineering in order to come up with common goals and common lanuage.

### Quiz

What can happen if you are not aware of, and measuring your business objectives?

- [x] You fail to meet your companies legal contracts
- [ ] Your CEO will be very understanding and forgiving
- [x] You may lose important clients
- [x] The company can lose credibility

## Meeting different business objectives

- using the same language
- speak clearly
- be prepared when speaking business objectives
- cautious and transparents
- higher commitment costs in time and $
- service disruptions can be rare depending on situation
- maintain relationships with other teams in the department
- before committing, make sure you have an idea of how to build, run, monitor and maintain a system that can achieve the desired metrics
- inform the rest of the business in the rescources required to do so

### Quiz

#### Q1: When should you say “no” to a contract with a high (Service Level Agreement) SLA for uptime?

- [x] When you know you can't meet it
- [x] When it calls for 100% uptime
- [ ] When it runs outside of AWS
- [ ] When you aren't measuring your uptime

#### Q2: Who is responsible for meeting business objectives?

- [ ] The software developers
- [x] Everyone
- [ ] The DevOps team
- [ ] The Operations team

## Uptime

- measured as a %-age of time a sys is avail
- 99.9% is typical - measured over the course of a month
- you can keep it avail, and the customer is okay with it
- depending on SLA, that might be less or more than enough
- requires diligence to meet SLA
  - need to track it
- need redundancy thru-out service
- referred to as a "number of nines" for uptime
  - three nines: 99.9% of uptime
  - three and a half nines: 99.95%
- single point of failure is any point that would cause the sys to fail
  - "murphy's law or probability is the term used
  - this would cause us to miss an SLA
  - db, networking, servers, caches, staff (people who can fix it)
- [chart of number nines](https://en.wikipedia.org/wiki/High_availability)

### Example: Calculate Uptime

- allowed downtime - number of minutes allowed without violating SLAs
- even though 30 days isn't always the case, it's used
- how much downtime would a 99% SLA allow for?
  - (30 days) x (24 hrs/day) x (60 minutes/hr) x (SLA %-age)
  - (30) x (24) x (60) x (.99) =  42768 minutes up in a month
  - 43200 total minutes in a month
  - 43200 - 42768 = 432 minutes
  - 7 hours 12 minutes a month for 99% uptime

### Quiz 1: Uptime

#### Q1: 99.9% uptime means a system is down for no more than how mnay minutes per month?

- (30) x (24) x (60) x (.001) = 43.2 minutes per month 
- ~45 min

#### Q2: What SLA does AWS put on durability of objects in S3?

- I believe it was eleven 9's:
  - 99.999999999%
  - correct

## Downtime

- has important caveats as opposed to just "opposite of uptime"
- important to keep in mind, when guaranteeing a contract:
  - you don't have to provide all services to your contract holder
    - can say: "use a third-party service which provides 99.9% uptime"
  - if you're guaranteeing 99.9% uptime, and you have a third-party which also provides another 99.9% this would only work for your SLA if both of those 99.9% fell within the same window of time
    - should they fall outside that window, then you've missed your SLA window
    - gets hairier when you add more third-parties to the mix
- can indicate this in writing in your agreements, but often times companies won't go for it

### Downtime exceptions

- costs more to have HA platform
- pass this cost thru to your customers
- probably don't use it 24 hours a day
- scheduled maintenance
  - 3 AM on a saturday morning?
  - may not care about that window of time
- if you can agree on certain times of days where the service isn't needed, it would give a cost-efficiency and allow for your "downtime" to requirements to be met as well
- less complex and less-costly sys is possible this way
- degraded service is an option to:
    - possibly there are aspects perhaps that aren't _really_ required, but are nice to have during non-peak hours
- Force Majeure - "act of God" are things beyond anyone's control
  - Earthquake
  - Floods
  - Pandemics
- Downtime isn't too simple, how do you determine downtime?
  - carefully.. maintenance windows?
  - calculate it before the question gets asked
  - in writing, includes all exclusions and mentioned in any contracts in place

### Quiz 2: Downtime

#### Q1: Which services are likely non-critical in a product?

- [X] Twitter feed
- [X] Emoji
- [ ] Data storage
- [ ] Authentication

#### Q2: Which window would be most acceptable for excused dowtime for an international business application?

- ( ) 1am-3am in the user's timezone
- ( ) 1am-3am EST
- ( ) The first day of the month, 1am-4am
- (O) The first Saturday of the month, 1am-4am
  - this is the best option since you can't quite coordinate timezones for different regions, but Saturday is typically a safe bet and bleeds into Sunday at the worst case

## Exercise: SLA

- asked to create an SLA
- represent the uptime and downtime of the platform
- list of requirements below:
  - Email service provider: 99.9%
  - DNS provider: 99.99%
  - Authentication service provider: 99.9%
  - AWS services: 99.9%
  - Twitter feed: 99%

### Section 1:

- include your own applications, what do you create


### Section 2:

- third-party

### Section 3:

- non-critical components

## Solution: SLA

### Calculating Availability

**Write up an SLA for your platform that breaks down acceptable amounts of downtime for your application and for third party services separately. Also define periods of excused downtime and caveats for reduced functionality of non-critical components.**

#### **ABC Company Hosted Applications**

ABC Company shall provide general platform uptime of 99.9%. This shall exclude a regular maintenance window from 1am - 5am CST every Sunday, as well as windows from 1am - 5am on other days provided that ABC provides 24 hours of notice.

#### **ABC Company Critical Third-Party Components**

Some components of our system are beyond our control, and thus, we can only pass through the provided SLA of these vendors. We only work with vendors who provide an SLA of 99.9% or better for all critical services.

#### **ABC Company Non-Critical Components**

Those components deemed non-critical (listed below) are subject to a 95% uptime SLA. These components are not core to our offering, but provide certain "nice to have" functionality. In cases where these services are not available, our platform shall continue to operate and provide core functionality as normal.


## Recovery Time Objective (RTO)

The maximum duration of a service interruption

- time agreed to in SLA
- possible to fail to meet RTO, penalties would come into play
- measured per incident rather than on a monthly basis
- should be small enough so that one outage should still be within your SLA
  - o/w you could meet your RTO SLA but still meet your overall SLA
- as with uptime, you need to consider what part of your RTO you can support
- all critical parts of your platform must be designed for recovery within your RTO timeframe
- things to consider:
  - don't forget about human factor
    - getting people out of bed
    - searching for documentation
  - all components of a failure and recovery
  - sum of the time it takes to recover from a failure
    - if it takes 2 hours to recover from a database backup, cannot have a 1 hour RTO
  - make sure your staff knows how to recover your system
  - get in touch with a Subject Matter Expert (SME) as needed

### Quiz 3: RTO

#### Q1: What is required to support a 2 hour RTO?

- [x] 24/7 support team
- [x] Remote access to your systems
- [ ] Multi-Region applications
- [x] Documented recovery procedures

#### Q2: if your database goes down at 7:00pm and your application continues to run until 7:20pm but is failing to store the data it receives, and then the database is brought back online at 8:00pm, what is the RTO of the outage?

- (O) 1 hour
- ( ) 20 minutes
- ( ) 40 minutes
- ( ) 1 hour 20 minutes

### Quiz 4: Exercise RTO

When determining what type of RTO you can suport, there are many factors to take into consideration. By not accounting for components that impact your RTO, you put your ability to meet the obligation at risk.

Let's consider the situation where a database runs out of disk space in the middle of the night.

Some items to consider are:

- Problem happens
- An amount of time passes before an alert triggers
- Alert triggers on-all staff
- On-call staff may need to get out of bed, get to a computer, log in, log onto VPN
- On-call staff starts diagnosing the issue
- Root cause is discovered
- Remediation started
- Issue is fixed

Estimate how much time each of the above is likely to take. Use that data to create a timeline of the incident. What is the RTO of this incident?
| Issue | Duration (minutes) |
| - | - |
| Problem happens | 0 |
| An amount of time passes before an alert triggers | 40 |
| Alert triggers on-all staff | 5 |
| On-call staff may need to get out of bed, get to a computer, log in, log onto VPN | 15 |
| On-call staff starts diagnosing the issue | 15 |
| Root cause is discovered | 20 |
| Remediation started | 15 |
| Issue is fixed | 60 |
|  |  |
| Total | 170 |

**Just under 3 hours, RTO ~= 3 hours**

## Recovery Point Objective (RPO)

RPO is the maximum amount of time that your system can lose data for. RPO is **not** tied to whether your system is avail, it is a measure of the window of time that you may lose data in. If you take a database snapshot at midnight every day and you have a data corruption issue at 7:00am requiring you to restore from backup, you have a 7 hour RPO. It may take you until 8:00am to restore service, but as long as your data loss stops at 7:00am, that is when your RPO window closes.

- losing data is always worse than not being able to take it in the first place
  - imagine populating a cart and then losing that cart
  - this is probably worse than not being able to add to cart to begin with
- most data is lost in 1 of 2 ways:
  - if sys is up and avail, but some failure is preventing it from durably storing data sent to it, perhaps it's 100% full
    - systems tend to only have 1 shot of capturing data
    - it may be possible to detect what data is missed thru logs, but can be tricky and time consuming and not accurate
  - if you restore from back up and the backup is old, you lose data
    - if you back up once per hour then your RPO is 1 hour
    - if your system goes down at 1:45 then to use the 1:00 data means you'll lose 45 minutes worth of data, and the RPO would be 45 minutes for that window
- having redundant systems is the key to avoiding recovery in the first place
- HA services keep things running and void disruptions
- S3's version buckets helps with keeping older versions
  - undo and revert
- services like RDS and DynamoDB offer _Point in Time Data Recovery_
  - this means they essentially have a continual backup
  - massive data corruption at 1:45 could be restored back to 1:44 and have very little data loss and small RPO
  - if data loss occurs at 1:45 but you don't realize until 2:15, it's still 30 min RPO sadly

### Quiz 4: RPO

#### Q1: If you have an RPO of 6 hours, how often must you store a new backup?

- [ ] 3 hours
- [x] 6 hours
- [ ] 9 hours

#### Q2: If your database goes down at 7:00pm and your application continues to run until 7:20p but is failing to store the data it receives, and the database is brought back online at 8:00p, what was the RPO of the outage?

- [x] 20 minutes
- [ ] 40 minutes
- [ ] 1 hour
- [ ] 1 hour 20 minutes

this question is a bit incomplete; it assumes that the data storage resumes at 7:20 whereas the way it's stated now is that it's only corrupted from 7:00p-7:20p

### Exercise: RPO

- create an RDS database w/ backups enabled
- restore that database to a point in time to demonstrate _Point in Time Recovery_

#### Instructions

- go to Amazon RDS through AWS
- _Create database_
  - standard create
  - MySQL db
  - Dev/Test (simpler database)
  - leave most settings
  - choose Burstable classes (saves money, other larger ones   cost money)
  - _Additional Configuration_
    - _Enable automatic backups_ if not already
  - _Create database_

#### More info

- takes a moment to create, can check the _Status_ column in the _Databases_ section for Amazon RDS
  - can see the blue banner at the top of the screen indicating the db's status
- once it's created, it goes to the next stage of _Configuring-enhanced-monitoring_
- after it's setup we have to let it run for a little bit, then we can revert it to a backup

### Solution

- once the database is created as shown above, you can _Restore to Point in Time_
  - click on the db in question
  - click the _Actions_ dropdown at the top right
  - select _Restore to Point in Time_
  - can select the time from either latest or custom point
  - this will require a rename as well as indicated; can also try to launch instance and it'll highlight the field required
- a new db will be created momentarily containing the historical backup created off of the current db
  - will know when it's ready based on its status

## Disaster Recovery

- RTO and RPO numbers apply to localized outages
- when setting RTO and RPO, consider worst case scenarios
- _Disaster Recover_ is used to describe a more widespread failure
- if you were typically to run a service in one region only, a Disaster Recovery event (DR) would inidcate you had a large enough failure to move your system to another region
  - DR usually involves the wholesale moving of your platform from one place to another
- outside AWS you might need to change data centers
- within AWS you can just move to a diff region
- DR can't happen if not planned for in advance
  - will likely have to wait for region to recover if no plan is in place
- consider all things needed to restart your platform in a new home
  - what saved state
  - what application software
  - what configuration info
- consider keeping a backup of documentation for just such a scenario in a separate system in either a diff region (in AWS) or similar (outside AWS)

### Video info

- all of the things needed to migrate your system:
  - db backups
  - server images
  - your code
  - server configurations (and instance types)
  - any other data repositories
  - network configurations
  - documentaiton
  - IP whitelisting
- if you forget something, or have an old verison, DR cutover can take longer or fail completely
- documentation is a must when doing a DR failover (f/o)
- practice a DR f/o
  - practice until you don't need to cheat
  - practice often
  - if you don't, you run the risk of realizing your DR documentation is out of data during a DR event
- how long do you wait?
  - when it happens you assume it'll come back online
  - when it doesn't come back, how long before you jump to your DR
- who decides on when a DR f/o should kick off?
  - establish the list of people responsible
  - those people should know they're on the list
- types of DR planning
  - **cold standby** - have all of the data you need in your DR location
    - but every server and db will need to be spun up to perform a DR recovery
  - **"pilot light"** plan - always has a bare min of some of the things always in your DR environment
    - example: active read replica of a db in your DR environment
  - **warm standby** scenario - min amount of everything running in your DR region
    - so you would have dbs and perhaps 1 of each server type always running in your DR region
  - **hot standby** - complete second environment running at all times ready to be cut over to
- must weigh costs of running all of these things in your region and cost of time spent capable of getting your DR region to handle your production traffic

### Disaster Recovery in AWS

- **Geographic Recovery** - DR plans are intended to cover major service disruptions
- a typical DR plan usually entails recovering your platform hundreds of miles way
  - enter multi-Region services
  - if running A/A in multiple regions, you're done!
  - it really depends on the services you utilize as to if that'll be the case
- example of easy and ready multi-Region services:
  - DynamoDB - can be configured easily
  - S3 - can be configured easily
  - IAM - (Global) multi-Region by default
  - CloudFront - can be configured easily
- CloudFront takes your content from your source and distributes it from multiple regions around the world
  - in this sense it is always multi-Region
  - however the origin is only in 1 region
  - if that region fails, your application is done
  - with CloudFront origin groups, you can define multiple S3 buckets in multiple regions as f/o buckets
  - if CloudFront can't get content from primary, it'll resort to backup
  - with this, it can survive a region failure
  - CloudFront can use multiple S3 buckets as the origin and f/o between them - these are referred to as **Origin Groups**
- DynamoDB can run in an A/A scenario with faily low cost and effort
- RDS can create cross-region read replicas
  - but it will still take work to use that region as an active db
  - need to promote the read replica and then point your application at this new instance
- S3 buckets can be replicated across regions
  - provides a mechanism to maintain current copies of S3 objects
  - tradeoff is essentially paying double for DR if necessary

### Quiz 5: Disaster Recovery

#### Q1: What do you need in a secondary recovery Region?

- [x] Database snapshots
- [x] S3 buckets
- [x] Application code
- [x] Documentation

#### Q2: How can CloudFront fail over to a DR region?

- (O) Using multiple S3 buckets in an Origin Group
- ( ) Using multiple CloudFront distributions
- ( ) Using multiple CloudFront Origins

### Exercise: Disaster Recovery

AWS CloudFront is a content distribution network. It allows for edge location spread around the world to cache content so that the original source only needs to be consulted after a certain amount of time has expired. CloudFront is a global AWS service and has the ability to serve files from an S3 bucket. It also can be configured to have a primary bucket and a backup bucket in case the primary is not available.

#### Objective

Create two S3 buckets in two different regions and then create a CloudFront distribution and create an Origin Group with both buckets and use that as the origin for the CloudFront distribution.

### Solution

- click _Create Distribution_
- select _Get Started_ under the _Web_ option
  - _RTMP_ option is being discontinued starting December 31, 2020
- clicked in the box to get the dropdown for _Origin Domain Name_: `udacity-s3-exercise-lazar.s3.amazonaws.com` from the original `udacity-s3-exercise` option
- left _Origin ID_ as default: `S3-udacity-s3-exercise-lazar`
- scroll to bottom _Create Distribution_
- clicked on the ID of the distribution that was created: `E38N4DIYHAR65Z`
- select the tab at the top _Origins and Origin Groups_
- select _Create Origin_ even though it looks like there already is one there
- add the other S3 bucket you're planning on using from the first textbox which turns into a dropdown
- once finished it'll bring you back to the CloudFront Distributions > `<yourdistroID>` i.e. `E38N4DIYHAR65Z`
- select _Create Origin Group_
- choose the two S3 buckets selected manually
- choose a f/o option, whatever you think is best, i.e. _503 Service Unavailable_
- create that bad boy
- missed a few steps, see full solution in next section

### Provided solution

Disaster Recovery

1. Create an S3 bucket configured to host a website in your "primary" region.
1. Create a second S3 bucket in a second region. This will be your "standby" region.
1. Create a CloudFront Web distribution. In the creation wizard, click in the box "Origin Domain Name," and you will see a list of your S3 buckets. Select your "primary" S3 bucket.
1. Leave the rest of the options as defaulted and click Create Distribution.
1. Wait for the distribution to be created.

Once your distribution is created:

1. Select it from the list and click Distribution Settings.
1. Select the "Origins and Origin Groups" tab.
1. Click "Create Origin"
1. Click in "Origin Domain Name" and select your "secondary" S3 bucket and click Create. (the distribution will take some time to update).

Once your distribution is updated:

1. Select it from the list and click Distribution Settings.
1. Select the "Origins and Origin Groups" tab.
1. Click "Create Origin Group"
1. Add both S3 buckets and click Create.

Once your distribution is updated:

1. Select it from the list and click Distribution Settings.
1. Select the Behaviors tab
1. Select the item and click Edit.
1. Change "Origin or Origin Group" to the newly create origin group and click "Yes, edit"

When your distribution is finished updating, you will have a CloudFront distribution that will automatically failover between AWS regions.

## Edge Cases

- once things are signed, keep the company in line with SLA goals
- gently remind them of what needs to be done
- not so uncommmon in Govt regulated positions
- not as common in non-Govt regulated positions
- keep DR and f/o in consideration when prioritizing and planning future work

### Quiz 6: Edge Cases

#### Q1: RTO/RPO/DR can easily be forotten by a business until when?

- [x] There is an outage
- [ ] Bonus time
- [ ] The quarterly meeting board
- [ ] The summer retreat

#### Q2: Which industries are more likely to need a solid Disaster Recovery plan (pick 2)?

- [x] Financial industry
- [ ] Social media industry
- [x] Medical industry
- [ ] Gaming industry

## Recap

- created a variety of ways to meet business objectives
- measured uptime and downtime, not just opposites
  - maintenance and metrics
- RTO vs RPO, what they are and how they apply
- DR - disruption in your data center and region
  - practiced it, and which services are best suited for DR needs
- best pieces AWS has in those regions and AZs
- business objectives in Avail, reliab, and resil
- next monitoring for and reacting to failures within our systems

### Lesson Recap

- Uptime
- Downtime
- RTO
- RPO
- Disaster Recovery

### Lesson Objectives

- Calculate availability in terms of up and down time
- Set reasonable business metrics for RTO and RPO
- Make determinations on what types of DR plans a company - needs
- Implement a DR plan

Keep communication open, set reasonable and realistic goals; make expectations known, have a plan and make everyone away of the plan.  Keep in consideration time, money, and resources.

