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