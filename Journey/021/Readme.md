# SAA Prepare - Day 8:

## Snowball
- **Physical data transport solution** that helps moving **TBs or PBs** of data in or out of AWS
- Alternative to moving data over the network (and paying network fees)
- Secure, tamper resistant, uses **KMS 256 bit encryption**
- Tracking using **SNS** and text messages. E-ink shipping label
- Pay per data transfer job
- Use cases: large data cloud migrations, DC decommission, disaster recovery
- If it takes **more than a week** to transfer over the network, use Snowball devices

### Snowball Process
1. Request snowball devices from the AWS console for delivery
2. Install the snowball client on your servers
3. Connect the snowball to your servers and copy files using the client
4. Ship back the device when you’re done (goes to the right AWS
   facility)
5. Data will be loaded into an S3 bucket
6. Snowball is completely wiped
7. Tracking is done using SNS, text messages and the AWS console

## Snowball Edge
- Snowball Edges add **computational capability** to the device
- **100 TB capacity** with either:
    - Storage optimized – 24 vCPU
    - Compute optimized – 52 vCPU & optional GPU
- Supports a **custom EC2 AMI** so you can perform processing on the go
- Supports custom Lambda functions
- Very useful to pre-process the data while moving
- Use case: data migration, image collation, IoT capture, machine learning

## AWS Snowmobile (physical truck)
- Transfer more than **10 PB**, better than Snowball
- Transfer exabytes of data (1 EB = 1,000 PB = 1,000,000 TBs)
- Each Snowmobile has 100 PB of capacity (use multiple in parallel)


### Solution Architecture: Snowball into Glacier
- Snowball cannot import to Glacier directly
- You have to use **Amazon S3** first, and an **S3 lifecycle policy**

## AWS Storage Gateway!
- Bridge between on-premise data and cloud data in S3
- Use cases: disaster recovery, backup & restore, tiered storage

### Storage Gateway Types:
#### File Gateway
- using the **NFS** (Network File System, also used by EFS) and SMB protocol
- Supports S3 standard, S3 IA, S3 One Zone IA
- Bucket access using IAM roles for each File Gateway
- Most recently used data is cached in the file gateway
- Can be mounted on many servers, like EFS

#### Volume Gateway
- **Block storage** using **iSCSI protocol** backed by S3
- Backed by EBS snapshots which can help restore on-premise volumes
- **Cached volumes**: low latency access to most recent data
- **Stored volumes**: entire dataset is on premise, scheduled backups to S3

#### Tape Gateway
- Virtual Tape Library (VTL) backed by **Amazon S3** and **Glacier**
- **Back up data** using existing tape-based processes (and **iSCSI** interface)
- Works with leading backup software vendors

### AWS Storage Gateway Summary
- On premise data to the cloud => Storage Gateway
- File access / NFS => File Gateway(backed by S3)
- Volumes / Block Storage / iSCSI => Volume gateway(backed by S3 with EBS snapshots)
- VTL Tape solution / Backup with iSCSI = > Tape Gateway(backed by S3 and Glacier)

###Storage Gateway File Gateway – Hardware appliance
- Need virtualization to use a file gateway 
- Otherwise, you can use a File Gateway Hardware Appliance (buy it on amazon.com)
- Helpful for daily NFS backups in small data centers

### Exam Tips
If you have a small data center with no visualization capability and you still need to perform a daily NFS backup, then the answer is to buy and a **hardware appliance** and plug it onto your small data center, and using the hardware appliance, you can do your daily NFS backup

## Amazon FSx for Windows (File Server)
- EFS is a shared POSIX system for **Linux** systems
- **FSx** for Windows is a fully managed Windows **file** system **share** drive
- Supports **SMB** protocol & **Windows NTFS**
- Microsoft Active Directory integration, ACLs, user quotas
- Built on SSD, scale up to 10s of GB/s, millions of IOPS, 100s PB of data
- Can be accessed from your on-premise infrastructure
- Can be configured to be **Multi-AZ** (high availability)
- Data is backed-up daily to **S3**

## Amazon FSx for Lustre
- Lustre is a type of parallel distributed file system, for **large-scale computing**
- The name Lustre is derived from “**Linux**” and “cluster”
- Machine Learning, **High Performance Computing (HPC)**
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- **Seamless integration with S3**
- Can “read S3” as a file system (through FSx)
- Can write the output of the computations back to S3 (through FSx)
- Can be used from on-premise servers
- has a file system that is shared, with high IOPS, high throughputs, very low latency, and **integration with S3 as a backend**


## Storage Comparison
- S3: Object Storage，serverless
- Glacier: Object Archival
- EFS: Network File System for Linux instances, POSIX filesystem
- FSx for Windows: Network File System for Windows servers
- FSx for Lustre: High Performance Computing Linux file system
- EBS volumes: Network storage for one EC2 instance at a time. It is bound to a specific availability zone that you create it in. To change the AZ, need to create a snapshot, move that snapshot over, and create a volume from it
- Instance Storage: Physical storage for your EC2 instance (high IOPS)
- Storage Gateway: File Gateway, Volume Gateway (cache & stored), Tape Gateway
- Snowball / Snowmobile: to move large amount of data to the cloud, physically
- Database: for specific workloads, usually with indexing and querying



it’s better to **decouple** your applications,
    - using SQS: queue model
    - using SNS: pub/sub model
    - using Kinesis: real-time streaming model
- These services can scale independently from our application

## Amazon SQS – Standard Queue
- Fully managed service, used to **decouple applications**
#### Attributes:
- **Unlimited throughput**, unlimited number of messages in queue
- Each massage is **short-lived**, default retention of messages: 4 days, maximum of 14 days
- Low latency (<10 ms on publish and receive)
- Message in SQS have to small, limitation of 256KB per message sent 
- Can have **duplicate messages** (at least once delivery, occasionally)
- Can have **out of order** messages (best effort ordering)

### SQS – Producing Messages
- Produced to SQS using the SDK (SendMessage API)
- The message is persisted in SQS until a consumer deletes it

### SQS – Consuming Messages
- Consumers (running on EC2 instances, servers, or AWS Lambda)…
- Poll SQS for messages (receive **up to 10 messages** at a time)
- Process the messages (example: insert the message into an RDS database)
- Delete the messages using the DeleteMessage API

### SQS – Multiple EC2 Instances Consumers
- Consumers receive and process messages in parallel
- At least once delivery
- Best-effort message ordering
- Consumers delete messages after processing them
- We can scale consumers horizontally to improve throughput of processing

### integrate SQS with Auto Scaling Group (ASG)

<img src="SQS%20with%20Auto%20Scaling%20Group.png" alt="" width="800px">

- consumers are running on EC2 instances insdieof an Auto Scaling group
- A CloudWatch Metric that's available in any SQS queue
- set up a CloudWatch  Alarm
- scale the number of EC2 instances based on the amount of messages in the queue
- EC2 Instances are going to push a CloudWatch **Custom** Metric

### SQS to decouple between application tiers

<img src="decouple%20between%20application%20tiers.png" alt="" width="800px">

This is a robust and scalable type of architecture. In this architecture, we can scale the front-end accordingly, and we can scale the back-end accordingly as well, but **independently**. Because the SQS queue has unlimited throughputs and it has unlimited number of messages in terms of the queue, then you are really safe, and this is a robust and scalable type of architecture. (will come up in the exam )


### Amazon SQS - Security
#### Encryption:
- In-flight encryption using **HTTPS API**
- At-rest encryption using **KMS keys**
- Client-side encryption if the client wants to perform encryption/decryption itself
#### Access Controls: 
- **IAM policies** to regulate access to the SQS API
#### SQS Access Policies (similar to S3 bucket policies)
- Useful for **cross-account access** to SQS queues
- Useful for **allowing other services** (SNS, S3…) to write to an SQS queue

### SQS – Message Visibility Timeout
- By default, the “message visibility timeout” is 30 seconds
- After a message is polled by a consumer, it becomes invisible to other consumers
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the **ChangeMessageVisibility** API to get more time

### Amazon SQS – Dead Letter Queue
- Can set a threshold of how many times a message can go back to the queue
- After the **MaximumReceives** threshold is exceeded, the message goes into a **dead letter queue (DLQ)**
- Useful for debugging
- Good to set a retention of 14 days in the DLQ

### Amazon SQS – Delay Queue

- Delay a message (consumers don’t see it immediately) up to 15 minutes
- Default is 0 seconds (message is available right away)
- Can set a default at queue level (for all messages)
- Can override the default on send per messages using the **DelaySeconds** parameter

### Amazon SQS – FIFO Queue
- **Limited throughput**: 300 msg/s without batching, 3000 msg/s with batching
- **Exactly-once** send capability (by removing duplicates)
- Messages are processed in order by the consumer


## Amazon SNS
- The “event producer” only sends message to one SNS topic
- As many “event receivers” (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 10,000,000 subscriptions per topic
- 100,000 topics limit

### SNS integrates with a lot of AWS services
- Many AWS services can send data directly to SNS for notifications
- CloudWatch (for alarms)
- Auto Scaling Groups notifications
- Amazon S3 (on bucket events)
- CloudFormation (upon state changes => failed to build, etc)

### AWS SNS – How to publish
#### Topic Publish (using the SDK)
- Create a topic
- Create a subscription (or many)
- Publish to the topic
#### Direct Publish (for mobile apps SDK)
- Create a platform application
- Create a platform endpoint
- Publish to the platform endpoint
- Works with Google GCM, Apple APNS, Amazon ADM…

### Amazon SNS – Security (similar to SQS - Security)
#### Encryption:
- In-flight encryption using HTTPS API
- At-rest encryption using KMS keys
- Client-side encryption if the client wants to perform encryption/decryption itself
#### Access Controls: 
- IAM policies to regulate access to the SNS API
#### SNS Access Policies (similar to S3 bucket policies)
- Useful for cross-account access to SNS topics
- Useful for allowing other services ( S3…) to write to an SNS topic

### SNS + SQS: Fan Out
Using SNS to send the same message into many different SQS queues

<img src="Fan%20Out.png" alt="" width="800px">

- push once the message into SNS and receive in all SQS queues that are subscribers
- Fully decoupled, no data loss
- SQS allows for: data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue **access policy** allows for SNS to write
- **AWS limitation**: SNS cannot send messages to SQS FIFO queues

### Exam questions
#### Application: S3 Events to multiple queues
- You can have many different rules into your S3 Events, but for one specific type of event, for example:
    - For the same combination of: event type (e.g. object create) and prefix (e.g. images/) you can **only have one S3 Event rule**
- If you want to send the same S3 event to many SQS queues, use fan-out

**Q:** you want to have an application that reads events from an SQS queue for all the objects created into your S3 bucket, then you can only have one rule, that will send messages into only one SQS queue. But if you want to have many SQS queues, having these events or a copy of this events, then you cannot use just S3 event.
**A:** Use the fan-out pattern

 <img src="fan-out%20pattern.png" alt="" width="800px">

## AWS Kinesis
- Kinesis is a managed alternative to Apache Kafka
- It is a big data streaming tool which allows you to collect application logs, metrics, IoT, clickstreams
- Great for “real-time” big data
- Great for streaming processing frameworks (Spark, NiFi, etc…)
- Data is **automatically replicated to 3 AZ**


- Kinesis Streams (main focus on the exam): low latency streaming ingest at scale
- Kinesis Analytics: perform real-time analytics on streams using SQL
- Kinesis Firehose: load streams into S3, Redshift, ElasticSearch…

## Kinesis Streams
- Streams are divided in ordered Shards / Partitions (a Shard is basically think of it as one little queue)
- Data retention is 1 day by default, can go up to 7 days
- Ability to reprocess / replay data (A big difference with SQS, SQS once the data was consumed it was gone. But with Kinesis the data is still there)
- Multiple applications can consume the same stream (like SNS, oen producer to many consumers)
- **Real-time processing** with scale of throughput
- Once data is inserted in Kinesis, it **can’t be deleted** (immutability)

### Kinesis Streams Shards
- One stream is made of many different shards
- 1MB/s or 1000 messages/s at write PER SHARD
- 2MB/s at read PER SHARD
- Billing is per shard provisioned, can have as many shards as you want
- Batching available or per message calls.
- The number of shards can evolve over time (reshard / merge)
- Records are ordered per shard

### AWS Kinesis API – Put records
• PutRecord API + **Partition key** that gets hashed
• The same key goes to the same partition (helps with ordering for a specific key)
• Messages sent get a “sequence number”
• Choose a partition key that is highly distributed (helps prevent “hot partition” - if your key wasn't distributed, then all your data will go through the same shard and one shard will be overwhelmed)
    • user_id if many users
    • **Not** country_id if 90% of the users are in one country
• Use Batching with PutRecords to reduce costs and increase throughput
• **ProvisionedThroughputExceeded** if we go over the limits
• Can use CLI, AWS SDK, or producer libraries from various frameworks

### AWS Kinesis API – Exceptions
### ProvisionedThroughputExceeded Exceptions
- Happens when sending more data (exceeding MB/s or TPS for any shard)
- Make sure you don’t have a hot shard (such as your partition key is bad and too much data goes to that partition)
#### Solution:
- Retries with backoff
- Increase shards (scaling)
- Ensure your partition key is a good one

### AWS Kinesis API – Consumers
- Can use a normal consumer (CLI, SDK, etc…)
- Can use Kinesis Client Library (in Java, Node, Python, Ruby, .Net)
    - KCL uses DynamoDB to checkpoint offsets
    - KCL uses DynamoDB to track other workers and share the work amongst shards

### Kinesis Security
- Control access / authorization using IAM policies
- Encryption in flight using HTTPS endpoints
- Encryption at rest using KMS
- Possibility to encrypt / decrypt data client side (harder)
- VPC Endpoints available for Kinesis to access within VPC

## AWS Kinesis Data Firehose

<img src="Kinesis%20Data%20Firehose%20Diagram.png" alt="" width="800px">

- Fully Managed Service, no administration, automatic scaling, serverless
- Load data into **Redshift** / **Amazon S3** / **ElasticSearch** / **Splunk**
- **Near Real Time**
    - 60 seconds latency minimum for non full batches
    - Or minimum 32 MB of data at a time
- Supports many data formats, conversions, transformations, compression
- No pay for provisioning firehose, only pay for the amount of data going through Firehose

### Kinesis Data Streams vs Firehose
#### Streams
- Going to write custom code (producer / consumer)
- Real time (~200 ms)
- Must manage scaling (shard splitting / merging)
- Data Storage for 1 to 7 days, replay capability, multi consumers
#### Firehose
- Fully managed, send to S3, Splunk, Redshift, ElasticSearch
- **Serverless** data transformations with Lambda
- Near real time (lowest buffer time is 1 minute)
- Automated Scaling
- No data storage

## Kinesis Analytics
- Conceptually, Kinesis Analytics can take data from kinesis data streams and kinesis data firehose and perform some queries
- Perform **real-time** analytics on Kinesis Streams using **SQL**
- Kinesis Data Analytics:
    - Auto Scaling
    - Managed: no servers to provision
    - Continuous: real time
- Pay for actual consumption rate
- Can create streams out of the real-time queries

## Data Ordering
### Ordering data into Kinesis
- send using a “Partition Key” value of the “something_id”
- The same key will always go to the same shard

### Ordering data into SQS
- If you don’t use a Group ID, SQS FIFO messages are consumed in the order they are sent, **with only one consumer**
- You want to scale the number of consumers, but you want messages to be “grouped” when they are related to each other
- Then you use a Group ID (similar to Partition Key in Kinesis)

## SQS vs SNS vs Kinesis

<img src="SQS%20vs%20SNS%20vs%20Kinesis.png" alt="" width="800px">

## Amazon MQ
- SQS, SNS are “cloud-native” services, and they’re using proprietary protocols from AWS.
- Traditional applications running from on-premise may use open protocols such as: **MQTT**, **AMQP**, **STOMP**, **Openwire**, WSS
- When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- Amazon MQ = managed Apache ActiveMQ
- Amazon MQ doesn’t “scale” as much as SQS / SNS
- Amazon MQ runs on a dedicated machine, can run in HA with failover
- Amazon MQ has both queue feature (~SQS) and topic features (~SNS)