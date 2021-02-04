# Lesson 5: Monitor, React, and Recover

## Lesson Outline

- Monitoring
- Alerting
- Recovering
- Automating

## Eyes and Ears

- unless you monitor, you're blind
- without alerts, you're deaf
- systems that can reach out when in need of help, or can see problems coming is critical to meeting business goals
- automation can help keep your system running seemelessly
- things break
- expect things to break
  - ask: "what should you do when it does break?"
- the more important, the bigger the problem it is when it does break
- create systems that are observable, and then actually do it
- create active monitoring = eyes
- create alerting systems = ears

### Quiz 1: Eyes and Ears

#### Q: Which components of your platform should you monitor?

- (O) Any that are important
- ( ) Those that are expensive
- ( ) Those that are multi-Region

## Recovering all your systems

- know how to build a system that's easy to monitor
- keep monitoring in mind earlier in development
- "how would you diagnose an issue with an application?"
- "how would you understand its health?"
- "what are its choke points?"
- "how would you identify them?"
- "what would you do if something breaks?"
- 'chaos engineering' are techniques employed by organizations to intentially cause breakage in their environments (envr) in a controlled manner
- if it's resilient, why not terminate a random server?
  - see how it reacts
  - gain otherwise (o/w) unattainable insight from these events
- once alerted, how do you find the actual problem?
- think from the start how you would debug your platform
  - extensive logging
  - metric collection
  - be able to quickly analyze your logs and metrics
  - recognize what "normal" looks like
  - be sure you can recover from failure, but also quickly and safely
  - how do you know if your backups are complete?
  - practice these practices!

### Quiz 2: Recovering all your systems

#### Q1: Name on reason to monitor your non-production environments.

- (O) To practice monitoring your production environments - if you can monitor the non-production it'll demonstrate what you can do for your production ones
- ( ) All environments should be monitored - only important ones
- ( ) You can't monitor production if you don't monitor non-production - not true, you can choose what you monitor

#### Q2: Should you ever intentionally disrupt a production service?

- (O) Yes, chaos engineering is a strong practice
- ( ) Yes, live on the edge
- ( ) No, never

## Monitoring in AWS

- "it's not production if you're not monitoring it"
  - if you're not monitoring, you're begging for trouble
- not as exciting, but is important
- highly reliab and avail systems still fail
- succeeding can still lead to failure
  - new system strains can occur when you bump up a factor of users
  - w/o monitoring you'll miss the growing pain identifications that occur
- **CloudWatch** is the primary AWS service for monitoring
  - has many pieces that work together
  - provide an overall view of your platform and services
  - CloudWatch metrics are the main repository of monitoring metrics within AWS
  - many metrics you get in AWS are from AWS and only avail thru AWS
    - what does the CPU utilization look like in your RDS db?
    - how many msgs are in an Amazon Simple Queue Service (SQS)
  - CloudWatch makes those metrics avail to you
- be aware of what metrics are avail
- can make custom metrics as well
- **CloudWatch Logs** is a service for storing and viewing text-based logs
  - AWS services that generate logs (Lambda, Gateway, etc) automatically log into CloudWatch logs
  - custom applications can log to _CloudWatch Logs_ 
- **CloudWatch Insights** is a powerful tool for searching through these logs (both internally and externally generated logs)
- **CloudWatch Synthetics** are health checks that you can create for your HTTP endpoints
  - "is this service responding correctly to your HTTP requests?"
- **CloudWatch Dashdboards** - dashboards are a great way to create at-a-glance views of your system's health
  - problem lack the time to investigate system health every day
  - instead create dashboards to give you a good overview
- **CloudWatch Alarms** - alerting mechanism for _CloudWatch Metrics_
- list of [AWS Services that Publish CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html)

### Quiz 3: Monitoring in AWS

#### Q1: Which are metrics available for a Lambda function?

- [x] Invocation count
- [x] Duration
- [ ] Cost
- [x] Error Count

the best way to see the solution of this is to follow the link of [listed AWS CloudWatch metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html) then select the [AWS Lambda Metrics](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html#monitoring-metrics-invocation) link and navigate to the page to see what's all covered under Lambda for example

#### Q2: Which SQS metric tells you how many messages are waiting in the queue?

- ( ) ApproximateAgeOfOldestMessage
- (O) ApproximateNumberOfMessagesVisible
- ( ) NumberOfEmptyReceives
- ( ) NumberOfMessagesReceieved

Explicit defintion: 

**ApproximateNumberOfMessagesVisible**: 

The number of messages available for retrieval from the queue.

Reporting Criteria: A non-negative value is reported if the queue is active. 

Source:

Again, navigate from the [listed AWS CloudWatch metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html) to the [Monitor Amazon SQS Queues Using CloudWatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-monitoring-using-cloudwatch.html) then navigate to [Available CloudWatch metrics for Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html) to find instructions on what metrics are avail and what they're titled.

### Exercise: Monitoring in AWS

- create and run a Lambda function:
  - Lambda -> Functions
  -  _Create function_
  - _Author from scratch_
  - _Function name_: `udacity-monitoring-exercise`
  - _Runtime_: doesn't make a diff, but whatever language you're comfortable with; `Node.js 12.x` is the example they went with (javascript)
  - create it
- now configure test event:
  - scroll down to see **_Function code_**
  - contains boilerplate code, just prints out a log msg
- then "invoke" the Lambda function
  - at the top select the empty dropdown labeled _Select a test event_ -> _Configure test event_
    - this is the data that's passed into your Lambda function, but since it's a boilerplate Lambda function, this doesn't make a difference
  - name it
  - _Create event_
- select the new event from the _Select a test event_ dropdown
- click _Test_ 
- this will generate a log and the details of that log will be presented in a banner at the top
- can be run multiple times; do so several times
- navigate over to CloudWatch after and look at the metrics there

#### Notes:

- can add all of the following fields to a CloudFront monitoring query:
  - Invocations
  - Duration
  - Errors
  - Throttles
  - ConcurrentExecutions
- all of which were avail from my created Lambda function and are able to be plotted as a separate widget on the CloudFront dashboard

### Solution

#### Create Lambda function

1. Go to Lambda -> Create function.
1. Select Author from scratch, name your lambda function "udacity" and leave the remaining options at their default.
1. Click Create function.

This will create a Lambda function with the following basic code:

``` java
exports.handler = async (event) => {
    // TODO implement
    const response = {
        statusCode: 200,
        body: JSON.stringify('Hello from Lambda!'),
    };
    return response;
};
```

#### Run Lambda function

1. Configure a test event
1. Name the test and click Save
1. Click the Test button several times

#### Monitor

1. Go to the CloudWatch service. Then select Metrics -> Lambda
1. By Function Name
1. Select the function name of "udacity" and "Invocations."

You can see Lambda Monitoring and CloudWatch Metrics Monitoring

## Alerting

- CloudWatch has many components including **Alerting**
  - does not perform alerting itself
  - integrates with other services to do it
  - CloudWatch Alarms is where you describe the conditions
    - "CPU utilization over 85%.. do something"
    - "if db CPU utilization one-min average (avg) is over 85% for 3/5 minutes, do something"
- consider momentary spikes of utilization
  - are they impactful?
- what does "do something" mean?
  - you've reached a "do something" condition
  - centered around SNS topics
    - send emails or text messages
    - trigger Lambda functions
    - send HTTP requests
    - last 2 are key intergration points
    - 3rd party options as well:
      - pager duty
      - victorOps
      - alarms continue to escalate until someone deals with it
- alerting when problems occur is critical
- alerting when problems are about to occur is far better
- strong understanding of your architecture and design is critical in setting thresholds for alerting

### Quiz 4: Alerting

#### Q1: What service does CloudWatch Alarms send messages to?

- [x] SNS
- [ ] SQS
- [ ] SES
- [ ] EC2

#### Q2: How do you determine what amount of ree space in a database is low enough to alert on?

- [x] Look at historical usage and set the level so that you have more than a week to react
- [ ] 10GB free is the proper level to alert at
- [ ] 1GB free is the proper level to alert at
- [ ] Look at historical usage and set the level so that you have 2 days to react

### Exercise: Alerting

Alerting is the layer on top of monitoring. Alerting raises the flag that something is wrong and requires attention. 

In this exercise, create a Lambda function and set up an alert on a Lambda functions invocation in CloudWatch Alarms to email you anytime that the Lamdba is run.

### Solution

#### Create Lambda Function

1. Go to Lambda -> Create function.
1. Select Author from scratch, name your lambda function "udacity" and leave the remaining options at their default.
1. Click Create function.

This will create a Lambda function with the following basic code:

```java
exports.handler = async (event) => {
    // TODO implement
    const response = {
        statusCode: 200,
        body: JSON.stringify('Hello from Lambda!'),
    };
    return response;
};
```

#### Create Alarm

1. Go to CloudWatch -> Alarms -> Create Alarm.
1. Click Select metric -> Lambda -> By function name.
1. Select "udacity" Invocations.
1. Click "Select metric."
1. Change Period to One minute.
1. Set Condition to Greater than 0, and click Next.
1. Under "Select an SNS topic," choose "Create new topic" and add your email address and click Create topic, then click Next.
1. Name and describe the Alarm, then finish creating the alarm.
1. Check your email. You will need to confirm your subscription to the SNS topic.

#### Testing
NOw go back to your Lambda function and click the "Test" button a few times. Give CloudWatch a few minutes and then check your email for the alarm notification.

Email Alert:

```txt
You are receiving this email because your Amazon CloudWatch Alarm "udacity-alerting-exercise-2" in the US East (N. Virginia) region has entered the ALARM state, because "Threshold Crossed: 2 out of the last 2 datapoints [1.0 (15/01/21 18:51:00), 1.0 (15/01/21 18:50:00)] were greater than the threshold (0.0) (minimum 1 datapoint for OK -> ALARM transition)." at "Friday 15 January, 2021 18:54:34 UTC".

View this alarm in the AWS Management Console:
https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1#s=Alarms&alarm=udacity-alerting-exercise-2

Alarm Details:
- Name:                       udacity-alerting-exercise-2
- Description:               
- State Change:               OK -> ALARM
- Reason for State Change:    Threshold Crossed: 2 out of the last 2 datapoints [1.0 (15/01/21 18:51:00), 1.0 (15/01/21 18:50:00)] were greater than the threshold (0.0) (minimum 1 datapoint for OK -> ALARM transition).
- Timestamp:                  Friday 15 January, 2021 18:54:34 UTC
- AWS Account:                765079372953
- Alarm Arn:                  arn:aws:cloudwatch:us-east-1:765079372953:alarm:udacity-alerting-exercise-2

Threshold:
- The alarm is in the ALARM state when the metric is GreaterThanThreshold 0.0 for 60 seconds.

Monitored Metric:
- MetricNamespace:                     AWS/Lambda
- MetricName:                          Invocations
- Dimensions:                          [FunctionName = udacity-cloudfront-exercise]
- Period:                              60 seconds
- Statistic:                           Average
- Unit:                                not specified
- TreatMissingData:                    missing


State Change Actions:
- OK:
- ALARM: [arn:aws:sns:us-east-1:765079372953:Udacity_Default_CloudWatch_Alarms_Topic]
- INSUFFICIENT_DATA:
--
```

## Recovering From Failure

- failure will happen
  - someone will update your production infrastructure thinking they are working on your dev environment
  - someone will forget to pay the bill
- figure out why
  - some are simple solutions, especially if you have good  alerts
  - if disk space is consumed, check consumption patterns
    - if normal, add more, be done with it
  - if disk space is done, but you're weirdly short your   expectation
    - check CloudWatch metrics and see if there has been a  sharp increase
    - why is there an increase
    - understanding what has changed is key
  - what can change?
    - company changes
    - logs might have history
    - CloudTrail has a complete history of all AWS changes
    - internal vs external changes (did a third-party vendor make a change?)
    - time can break a system
      - SSL certificates
      - licenses
- fix it
  - now that it's figured out.. fix it
  - monitoring can help with future problem prevention

### Quiz 5: Recovering From Failure

#### Q1: What AWS service tracks API requests made to your AWS account?

- [x] CloudTrail
- [ ] CloudWatch
- [ ] RDS
- [ ] SNS

#### Q2: If the cause of an issue is not immediately obvious, what things should you look at to find the cause?

- [x] Recent changes
- [x] Documentation
- [ ] Changes over the last 30 days - more recent changes are the focus
- [ ] Your AWS bill - won't have as good of a record as your logs or CloudTrail will have

### Exercise: Recovering From Failure

The key to recovering from failure is identifying the root cause as well as how and who/what triggered the incident.

In this exercise, enable CloudTrail on your AWS account to ensure that you have the data necessary to look at the history of your AWS account and determine what happened and when.

### Solution

#### Recovering From Failure
1. Go to CloudTrail -> Trails -> Create trail.
1. Name your CloudTrail and enter a name for a new S3 bucket.

You will now be able to refer to this CloudTrail log for a complete history of the actions taken in your AWS account. You can also query these logs with Amazon Athena, which lets you filter through large amounts of data with ease.

## Automating Recovery

- automate problem recovery
- if an EC2 instance fails because of an underlying cause, the solution is to create a new instance
- if automated recovery measures are executed more frequently than normal
- it's good to create lesser notifications to be aware of these outcomes
- figure out "Why" the disk space is sparce as opposed to just providing more disk space
- several AWS services auto-scale to help with automated recovery
  - DynamoDB
  - RDS disk space scaling
  - EC2 AutoScaling Groups
  - Lambda
- be sure to test it, software defects can occur even with recovery implementations, test first

### Chaos Engineering

- break things in production, on purpose
- if your system can handle it, why not encourage the failure to demonstrate it?
- if you were to terminate a couple of EC2 instances, what would happen?
  - good thing to ask a team
- example: running out of disk space over time?
  - examine your own use case
  - get some warning
  - doesn't need to be "wake-up" alert if it's done far enough ahead
  - how far ahead?
  - how much time do you need for a maintenance event
- why wait until things are actively broken when you can address it in advance

### Quiz 6: Automating Recovery

#### Q1: What services have autoscaling available?

- [x] RDS
- [x] DynamoDB
- [ ] ElastiCache
- [ ] ElastiSearch

#### Q2: What are risks of trying to automate recovery?

- [x] A false positive that triggered and automated recovery
- [x] Runaway automation that could make issues worse
- [ ] Too much free time
- [ ] Automating yourself and team out of a job

## Edge Cases

- just b/c an app or resource doesn't have automated or included monitoring/maintenance services, doesn't mean you should
  - consider creating your own
  - perhaps import a service from a third-party monitoring service
  - not everything is simple to monitor
- don't let customers tell you that you're down
- ensure coverage of all components of your platform
- make sure staff are trained and practiced-up on procedures and documentation, this'll ensure the highest uptime you can offer

### Quiz 7: Edge Cases

#### Q1: What should you do if there are not straightforward ways to monitor an important system component?

- [x] Find a more complex way
- [x] Alter the system to make it more easily monitorable
- [ ] Simply monitor the components around it
- [ ] Manually monitor this component

#### Q2: Which are crucial items to allow for systems to be recovered in a timely manner?

- [x] Documentation
- [x] People empowered to decide when systems require recovery
- [x] Configuration in place to allow for recovery

## Lesson Recap

- worked on monitoring systems
- learned about the equivalent AWS tools
- learned about CloudWatch metrics
- learned about CloudWatch Alerting functionality
- Learned some recovery mechanics
  - diagnosing problems
  - identifying them early in the development
  - seeing problems coming before they happen
- automating the recovery of our systems
- remember: always expect failures

### Lesson Recap

- Monitoring
- Alerting
- Recovering
- Automating

### Lesson Objectives

- Monitor AWS applications
- Alert on problems in applications
- Recover failures in your platform
- Understand testing and tradeoffs in automating recovery from failure

