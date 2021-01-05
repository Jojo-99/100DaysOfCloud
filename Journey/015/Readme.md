# SAA Prepare - Day 2
## Exam Tips - ASG for Solutions Architects
### ASG Default Termination Policy (simplified version):
1. Find the AZ which has the most number of instances
2. If there are multiple instances in the AZ to choose from, delete the one with the oldest launch configuration
-  ASG tries the balance the number of instances across AZ by default

### Lifecycle Hooks
[Lifecycle Hooks docs](https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html)
![](Lifecycle%20Hooks.png)
- By default as soon as an instance is launched in an ASG it’s in service.
- You have the ability to perform extra steps before
the instance goes in service (Pending state)
- You have the ability to perform some actions before the instance is terminated (Terminating state)

### Launch Template vs Launch Configuration
- Both:
    - ID of the Amazon Machine Image (AMI), the instance type, a key pair, security groups, and the other parameters that you use to launch EC2 instances (tags, EC2 user-data...)
- Launch Configuration (legacy):
    - Must be re-created every time
- Launch Template (newer):
    - Can have multiple versions
    - Create parameters subsets (partial configuration for re-use and inheritance)
    - Provision using both On-Demand and Spot instances (or a mix)
    - Can use T2 unlimited burst feature
    - Recommended by AWS going forward
    
## EBS
### EBS Snapshots
- Incremental – only backup changed blocks
- EBS backups use IO and you shouldn’t run them while your application is handling a lot of traffic
- Snapshots will be stored in S3 (but you won’t directly see them)
- Not necessary to detach volume to do snapshot, but recommended
- Max 100,000 snapshots
- Can copy snapshots across AZ or Region
- Can make Image (AMI) from Snapshot
- EBS volumes restored by snapshots need to be pre-warmed (using fio or dd command to read the entire volume)
- Snapshots can be automated using Amazon Data Lifecycle Manager

### EBS Migration
- EBS Volumes are only locked to a specific AZ
- To migrate it to a different AZ (or region):
    - Snapshot the volume
    - (optional) Copy the volume to a different region
    - Create a volume from the snapshot in the AZ of your choice
    
### EBS Encryption
- When you create an encrypted EBS volume, you get the following:
    - Data at rest is encrypted inside the volume
    - All the data in flight moving between the instance and the volume is encrypted
    - All snapshots are encrypted
    - All volumes created from the snapshot

- Encryption and decryption are handled transparently by AWS 
- Encryption has a minimal impact on latency
- EBS Encryption leverages keys from **KMS (AES-256)**
- Copying an unencrypted snapshot allows encryption
- Snapshots of encrypted volumes are encrypted
- How to encrypt an unencrypted EBS volume
    1. create snapshot from an unencrypted Volume (this snapshot is unencrypted)
    2. copy this snapshot, then you can encrypt this snapshot
    3. create a new volume from the copied snapshot
    
### EBS RAID Options

#### RAID 0 (increase performance)
- Combining 2 or more volumes and getting the total disk space and I/O
- one disk fails, all the data is failed
- Use cases
  - An application that needs a lot of IOPS and doesn’t need
  fault-tolerance
  - A database that has replication already built-in
    
- For example: two 500 GiB Amazon EBS io1 volumes with 4,000
  provisioned IOPS each will create a… 1000 GiB RAID 0 array with an available bandwidth of 8,000 IOPS and 1,000 MB/s of throughput

#### RAID 1 (increase fault tolerance)
- RAID 1 = Mirroring a volume to another, have to send the data to two EBS volume at the same time (2x network)
- If one disk fails, our logical volume is still working

- Use case:
    - Application that need increase volume fault tolerance
    - Application where you need to service disks

- For example: two 500 GiB Amazon EBS io1 volumes with 4,000
provisioned IOPS each will create a… 500 GiB RAID 1 array with an available bandwidth of 4,000 IOPS and 500 MB/s of throughput