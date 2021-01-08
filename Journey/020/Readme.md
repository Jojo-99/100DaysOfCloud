# SAA Prepare - Day 7: Advanced Amazon S3 & Athena, CloudFront & AWS Global Accelerator
## S3 MFA-Delete
- MFA (multi factor authentication) forces user to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3
- To use MFA-Delete, enable Versioning on the S3 bucket
- You will need MFA to
  - permanently delete an object version
  - suspend versioning on the bucket
- You won’t need MFA for
  - enabling versioning
  - listing deleted versions
- Only the bucket owner (root account) can enable/disable MFA-delete
- Only CLI can enable MFA-delete. Therefore you have to use the root Access Key. Use it to enable MFA delete and disable it!
- After MFA delete is enabled, you cannot delete a version permanently or suspend versioning from the console. You have to do it on CLI and provide the MFA device + code

## S3 Access Logs
- For audit purpose, you might want to log all access to S3 buckets.
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket.
- The access logs can be analysed with Amazon Athena
- Do not put your logging to your monitored bucket: logging loop!

## S3 Replication (CRR & SRR)
- After activating repliication, only new objects are async replicated (not retroactive)
- S3 Replication requirements
    1. Must enable versioning in source and destination
    2. Buckets can be in different accounts
    3. Copying is async
    4. Must give proper IAM permissions to S3
- User cases
    1. CRR: compliance, lower latency, replication across accounts
    2. SRR: log aggregation, live replication between production and test accounts

- Handling DELETE operations:
    1. If you delete without a version ID, it adds a delete marker, the marker not replicated.
    2. If you delete with a version ID, it deletes the version on the origin, but deleting action is not replicated either.
- You cannot "chain" replication. If bucket 1 has replication into bucket 2 and 2 into 3, bucket 3 does not replicate bucket 1

## S3 Pre-Signed URLs
- Can generate pre-signed URLs using SDK or CLI
    - For downloads (easy, can use the CLI)
    - For uploads (harder, must use the SDK)

- Valid for a default of 3600 seconds, you can change timeout with `--expires-in [TIME_BY_SECONDS]` argument. eg `--expires-in 300`
- Users given a pre-signed URL inherit the permissions of the person who generated the URL for GET / PUT
- Examples :
    - Allow only logged-in users to download a premium video on your S3 bucket
    - Allow an ever changing list of users to download files by generating URLs dynamically
    - Allow temporarily a user to upload a file to a precise location in our bucket
    
## S3 Storage Classes

- S3 Storage Classes can be configured at the object level, and a single bucket can contain objects stored across S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA, and S3 One Zone-IA

### S3 Standard – General Purpose
- High durability (99.999999999%) of objects across multiple AZ
- Sustain 2 concurrent facility failures
- Use Cases: Big Data analytics, mobile & gaming applications,content distribution…

### S3 Standard – Infrequent Access (IA)
- Suitable for data that is less frequently accessed, but requires rapid
access when needed
- Low cost compared to Amazon S3 Standard
- Sustain 2 concurrent facility failures
- Use Cases: As a data store for disaster recovery, backups…

### S3 One Zone - Infrequent Access (IA)
- Same as IA but data is stored in a **single AZ**, data lost when AZ is destroyed
- Low latency and high throughput performance
- Supports SSL for data at transit and encryption at rest
- Low cost compared to IA (by 20%)
- Use Cases: Storing secondary backup copies of on-premise data, or storing data you can recreate

### S3 Intelligent Tiering
- Same low latency and high throughput performance of S3 Standard
- Small monthly monitoring and auto-tiering fee
- Automatically moves objects between two access tiers based on changing access patterns
- Designed for durability of 99.999999999% of objects across multiple Availability Zones
- Resilient against events that impact an entire Availability Zone

### Amazon Glacier
- Low cost object storage meant for archiving / backup
- Data is retained for the longer term (10s of years)
- Alternative to on-premise magnetic tape storage
- Cost per storage per month ($0.004 / GB) + retrieval cost
- Each item in Glacier is called “Archive” (up to 40TB)
- Archives are stored in ”Vaults”

### Amazon Glacier & Glacier Deep Archive
#### Amazon Glacier – 3 retrieval options:
- Expedited (1 to 5 minutes)
- Standard (3 to 5 hours)
- Bulk (5 to 12 hours)
- Minimum storage duration of 90 days
#### Amazon Glacier Deep Archive – for long term storage – cheaper:
- Standard (12 hours)
- Bulk (48 hours)
- Minimum storage duration of 180 days

## S3 Lifecycle Rules
- Transition actions: It defines when objects are transitioned to another storage class.
    - Move objects to Standard IA class 60 days after creation
- Expiration actions: configure objects to expire (delete) after some time
    - Can be used to delete old versions of files (if versioning is enabled)
    - Can be used to delete incomplete multi-part uploads
- Rules can be created for a certain prefix (ex - s3://mybucket/mp3/*)
- Rules can be created for certain objects tags (ex - Department: Finance)

## S3 – Baseline Performance
- Amazon S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least 3,500 PUT/COPY/POST/DELETE and 5,500 GET/HEAD requests per second per prefix in a bucket.
- No limits to the number of prefixes in a bucket
- Example (object path => prefix):
  - bucket/folder1/sub1/file => /folder1/sub1/
  - bucket/folder1/sub2/file => /folder1/sub2/
  - bucket/1/file => /1/
  - bucket/2/file => /2/

- If you spread reads across all four prefixes evenly, you can achieve 22,000 requests per second for GET and HEAD

## S3 – KMS Limitation
- If you use SSE-KMS, you may be impacted by the KMS limits
- When you upload, it calls the **GenerateDataKey** KMS API
- When you download, it calls the **Decrypt** KMS API
- Count towards the KMS quota per second(5500, 10000, 30000 req/s based on region)
- Cannot request a quota increase for KMS
    
## S3 Performance
### Multi-Part upload:
- recommended for files > 100MB, must use for files > 5GB
- Can help parallelize uploads (speed up transfers)
- once all the parts have been uploaded it's smart enough to put them together back into the big file

### S3 Transfer Acceleration (upload only)
- Increase transfer speed by transferring file to an **AWS edge location** which will forward the data to the S3 bucket in the
target region
- Compatible with multi-part upload

### S3 Byte-Range Fetches
- Parallelize **GETs** by requesting specific byte ranges
- Better resilience in case of failures
- Can be used to speed up downloads
- Can be used to retrieve only partial
  data (for example the head of a file)
  
## S3 Select & Glacier Select
- Retrieve less data using SQL by performing **server side filtering**
- Can filter by rows & columns (simple SQL statements)
- Less network transfer, less CPU cost client-side

<img src="S3%20Select%20&%20Glacier%20Select.png" alt = ""  width="800px">

## S3 Event Notifications
- Can create as many “S3 events” as desired
- SNS - which is a simple notification service to send notifications and emails
- SQS - a simple queue service to add messages into a queue
- Lambda Function to generate some custom code
- If you want you to make sure every single event notification is delivered, you need to enable versioning on your bucket


## AWS Athena
- **Serverless** service to perform analytics **directly against S3 files**
- Uses **SQL** language to query the files
- Has a JDBC / ODBC driver
- Charged per query and amount of data scanned
- Supports CSV, JSON, ORC, Avro, and Parquet (built on Presto)
- Use cases: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc...
#### Exam Tip: Analyze data directly on S3 / How can we analyze our ELB Logs? / How can we analyze our VPC Flow Logs? => use Athena

## S3 Object Lock & Glacier Vault Lock
### S3 Object Lock
- Adopt a WORM (Write Once Read Many) model
- Block an object version deletion for a specified amount of time
### Glacier Vault Lock
- Adopt a WORM (Write Once Read Many) model
- Lock the policy for future edits (can no longer be changed)
- Helpful for compliance and data retention


## AWS CloudFront
- Content Delivery Network (CDN)
- Improves read performance, content is cached at the edge locations (216 Point of Presence globally)
- DDoS protection, integration with Shield, AWS Web Application Firewall
- Can expose external HTTPS and can talk to internal HTTPS backends

### S3 bucket as an origin
- For distributing files and caching them at the edge
### Enhanced security with OAI
Origin Access Identity(OAI), only allow the CloudFront to access your S3 bucket and nowhere else.

### CloudFront can be used as an ingress
Help you upload files into s3 from everywhere in the world through CloudFront

### Custom Origin (HTTP)
- Application Load Balancer
  1. Your alb must be public
  2. SG of ALB should allow all public IP of edge locations
- EC2 instance
  1. instance must be public
  2. SG of instances should allow all public IP of edge locations
- S3 bucket
- Any HTTP backend you want. e.g. things hosted at your on-premise infrastructure

## CloudFront Geo Restriction
- restrict who can access your distribution
    - white list: what countries are allowed
    - black list: what countries are not allowed
- The “country” is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

## CloudFront vs S3 Cross Region Replication
### CloudFront:
- Global Edge network
- Files are cached for a **TTL** (maybe a day)
- Great for static content that must be available everywhere

### S3 Cross Region Replication:
- Must be setup for **each region** you want replication to happen - good when you want only a few regions
- Files are updated in near **real-time**
- Read only 
- Great for **dynamic content** that needs to be available at low-latency in few regions

## CloudFront and HTTPS
- Viewer Protocol Policy: ensure traffic going into cloudfront is using https:
  1. Redirect HTTP to HTTPS (suggested, avoid failure)
  2. Use HTTPS only
- Origin Protocol policy (origin can be http or s3)
  1. HTTPS only
  2. Match viewer (if client request http, send request to origin user http, and vice versa)

- **NOTE:** S3 bucket "websites" don't support HTTPS

## CloudFront Signed URL/Signed Cookies
- CloudFront Signed URL are commonly used to distribute paid content through dynamic CloudFront Signed URL generation
- attach a policy with:
  - Includes URL expiration
  - Includes IP ranges to access the data from
  - Trusted signers (which AWS accounts can create signed URLs)
    
- How long should the URL be valid for?
  - Shared content (movie, music): make it short (a few minutes)
  - Private content (private to the user): you can make it last for years  
  

- Signed URL: access to one individual file
- Signed Cookies: access to multiple files

<img src="CloudFront%20Signed%20URL%20Diagram.png" alt="" width="800px"/>

## CloudFront Signed URL vs S3 Pre-Signed URL
#### CloudFront Signed URL
- CloudFront Signed URL allow access to a path, no matter the origin
- Account wide key-pair, only the **root account** can manage the signed url key-pair.
- Can filter by IP, path, date, expiration
- Can leverage caching features

#### S3 Pre-signed URL
- Issue a request as the person who pre-signed the URL (the client can **access directly** S3 bucket using that pre-signed URL)
- Uses the IAM key of the signing IAM principle
- Has limited lifetime

### Unicast IP vs Anycast IP
- Unicast IP: one server holds one IP address
- Anycast IP: all servers hold the same IP address and the client is routed to the nearest one

## AWS Global Accelerator
- Leverage the AWS internal network to route to your application
- **2 Anycast IP (static)** are created for your application
- The Anycast IP send traffic directly to Edge Locations
- The Edge locations send


- Works with Elastic IP, EC2 instances, ALB, NLB, public or private
- Consistent Performance
  - Intelligent routing to lowest latency and fast regional failover
  - No issue with client cache (because the IP doesn’t change)
  - Internal AWS network
- Health Checks
  - Global Accelerator performs a health check of your applications
  - Helps make your application global (failover **less than 1 minute** for unhealthy)
  - Great for disaster recovery 
- Security
  - only **2 external IP** need to be whitelisted
  - DDoS protection thanks to AWS Shield
  
## AWS Global Accelerator vs CloudFront
- They both use the AWS global network and its edge locations around the world
- Both services integrate with AWS Shield for DDoS protection

### CloudFront
- Improves performance for both cacheable content (such as images and videos)
- Dynamic content (such as API acceleration and dynamic site delivery)
- Content is served at the edge

### Global Accelerator
- Improves performance for a wide range of applications over TCP or UDP
- Proxying packets at the edge to applications running in one or more AWS Regions. (All the request still make it to our application end. There is no caching available)
- Good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP
- Good for HTTP use cases that **require static IP addresses**
- Good for HTTP use cases that required deterministic, **fast regional failover**