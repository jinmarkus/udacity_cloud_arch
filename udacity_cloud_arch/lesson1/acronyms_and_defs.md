# Acronyms

| Acronym/Initialism | Definition |
| - | - |
| ACL | Address Control List |
| AMI | Amazon Machine Image |
| avg | average |
| AZs | Availability Zones |
| CLI | Command Line Interface |
| DB | database |
| DHCP | Dynamic Host Configuration Protocol |
| DNS | Domain Name System |
| EC2 | (Amazon) Elastic Compute Cloud |
| envr | environments |
| f/o | failover |
| HA | Highly Avaiable |
| hw | hardware |
| IA | Infrequently Accessed |
| IAM | Identity and Access Management |
| igw | internet gateway |
| KMS | Key Management Service |
| msg | message |
| NAT | Network Address Translation |
| o/w | otherwise |
| RDS | (Amazon) Relational Database Service |
| RPO | Recovery Point Objective |
| RTO | Recovery Time Objective |
| S3 | (Amazon) Simple Storage Service |
| SDK | Software Development Kit |
| SLA | Service Level Agreements |
| SMTP | Simple Mail Transfer Protocol |
| SNS | (Amazon) Simple Notification Services |
| SQS | (Amazon) Simple Queue Service |
| SSL | Secure Sockets Layer |
| TLS | Transport Layer Security |
| VPC | Virtual Private Cloud |
| VPN | Virtual Private Network |
| WSL | Windows Subsystem for Linux |

# Definitions

| Term | Definition | Shorthand |
| - | - | - |
| Active/Active | this means you can run the same service in multiple regions while accessing the data they need locally while acting with some autonomy as opposed to having to reference a centralized database in another region | A/A |
| Amazon Machine Image | special type of virtual appliance used to create a virtual machine with the amazon elastic compute cloud (EC2); serves as basic unit of deployment for services delivered using EC2 | AMI |
| availability | a measure of time that a system is operating as expected (perentage usually) | avail |
| downtime | the time where your application is not an available serice during critical business operation | - |
| DynamoDB | AWS developed non-relational db | - |
| failover | a method for protecting from system failures, standby equipment takes over when the main system fail (uptime) | f/o |
| Fault Isolation | means of containing a fault in a system to a limited area | - |
| Force Majeure | term describing an event or circumstance that is completely unavoidable | - |
| IP whitelisting | allowing specific IP addresses only to access some resource | - |
| Multicast Networking | a networking protocol where traffic sent is in a "one-to-many" manner | - |
| Netowrk Fabric | a high speed network interconnect, where high volumes of traffic move over short distances | fabric |
| Network Latency | the time it takes network traffic to traverse back and forth over a network | latency |
| pilot light | old appliances like furnaces or water heaters had a small flame that was always burning to ignite a larger flame when needed; here it's used as a reference for Disaster Recovery, the small backup is waiting in case you need to re-establish an entire db | - |
| Recover Point Objective | max amount of time over which you would lose data | RPO |
| Recovery Time Objective | committment to the max amount of time the service will be down | RTO |
| Regulated industries | industries where the government sets strict rules and guidelines for data storage and operational practices | - |
| reliability | likelihood of something operating as expected at any given point in time (how often something fails) | reliab |
| resiliancy | a measure of system recoverability; how quickly can it be brought back online; graceful failure | resil |
| Simple Storage Services | AWS developed object store that can store an unlimited amount of data | S3 |
| Server-based Services | services that are existing applications that AWS provides as "managed services" and run on individual server instances | - |
| Simple Notification Service | Amazon SNS enables you to send messages or notifications directly to users with SMS text messages to over 200 countries | SNS |
| Secure Sockets Layer / Transport Layer Security | Transport Layer Security, and its now-deprecated predecessor, Secure Sockets Layer, are cryptographic protocols designed to provide communications security over a computer network | SSL / TLS |
| uptime | the time where your application is available and correctly functioning | - | 
| YAML Ain't Markup Language | human-readable data-serialization language; targets many of the same communications applications as Extensible Markup Language (XML) but has minimum syntax which differs from SGML | YAML |