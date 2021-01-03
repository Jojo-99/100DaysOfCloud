# Day12 - EFS vs EBS vs Instance Store

## EBS Volume (Elastic Block Store)
- <font color=#87CEFA>a network drive </font> that can attached to instances
- allow instances to persist data
- EBS is locked in one AZ
- can be attached to only one instance at a time
- To move a volume across AZ, need to snapshot it 
  
- Have a provisioned capacity (size in GBs, and IOPS)
  - You get billed for all the provisioned capacity
  - You can increase the capacity of the drive over time
  
- Root EBS Volumes of instances get terminated by default if the EC2 instance gets terminated. (you can disable that)
    
### EBS Volumes 4 types

* IOPS (I/O Ops Per Sec)
* Only GP2 and IO1 can be used as boot volumes

- <font color=#20B2AA>GP2 (SSD) </font>
    1. 1 GiB - 16 TiB
    2. Max IOPS is 16,000 (stops increasing when you have 16000/3 GiB)
    3. 3 IOPS per GiB, with minimum of 100, burstable to 3000 if you're below 1000Gib
    4. 16000 IOPS for volume sizes greater than 5333 GiB
    5. IO increases if the disk size increases
    
- <font color=#20B2AA>IO1 (SSD) </font> 
    1. 4 GiB - 16 TiB
    2. IOPS is provisioned (PIOPS), so Size of volume and IOPS are independent
    3. Min IOPS 100 - Max 64, 000 on Nitro instances; 32,000 on other instances.
    4. Max ratio of provisioned IOPS : GiB = 50:1
    5. Suitable for:
       - Critical business application needing sustained IOPS
       - More than 16,000 IOPS
       - Large DB workload
    
- <font color=#20B2AA>ST1 (throughput-optimised HDD) </font>
    1. Streaming workload; fast throughput at a low price
    2. Big data, Data warehouses, Log processing, Apache Kafka
    3. 500 GiB - 16 TiB
    4. Max IOPS is 500
    5. Max throughput of 500 MiB/s – can burst
- <font color=LightSeaGreen>SC1 (HDD) </font>
    1. Cold HDD, Infrequently accessed data
    2. lowest storage cost
    3. 500 GiB - 16 TiB
    4. Max IOPS is 250
    5. Max throughput of 250 MiB/s – can burst
  
## Instance Store
- Instance store is <font color=LightSkyBlue>physically</font> attached to the machine (EBS is a <font color=#87CEFA>network drive</font>) (= ephemeral storage)

- Instance store
  - <font color=LightSkyBlue>Pros:</font>
    - Better I/O performance (EBS gp2 has an max IOPS of 16000, <font color=HotPink>IO1 of 64000</font>), Very High IOPS (because physical)
    - Good for buffer / cache / scratch data / temporary content
    - Data survives reboots
  - <font color=LightSkyBlue>Cons:</font>
    - On stop or termination, the instance store is lost
    - Once you provisioned your local EC2 instance store, you can’t resize the instance store or add new ones
    - Backups must be operated by the user, risk of data loss if hardware fails
  

## EFS – Elastic File System
- Managed NFS (<font color=LightSkyBlue>network file system</font>) that can be mounted on many EC2 in multi-AZ
- Each mount point has an AZ, a subnet, a security group
- Highly available, scalable
- Mount targets provide NFSv4 endpoints to mount your EFS file system.
- Use cases: content management, web serving, data sharing, Wordpress
- <font color=LightSkyBlue>Only work with Linux</font> (POSIX) (not Windows)
- Uses security group to control access to EFS
- File system scales automatically, pay-per-use, no capacity planning!
- EFS pricing
  - expensive (<font color=HotPink>3 x gp2</font>)
  - pay for what you use (compared with EBS: pay for what you provision)

### EFS Scale
- 1000s of concurrent NFS clients
- 10GB/s throughput
- Grow to Petabyte-scale network file system, automatically
  
- <font color=LightSeaGreen>Performance mode</font> (set at EFC creation time)
  1. General purpose (default): low latency, for web servers, CMS
  2. Max I/O - higher latency, good throughput, highly parallel: for big data, media processing.
- <font color=LightSeaGreen>Storage tier</font> (with lifecycle management to move files after N days)
  1. Standard: frequently accessed files
  2. Infrequent access (EFS-IA): cost to retrieve files, lower price to store

