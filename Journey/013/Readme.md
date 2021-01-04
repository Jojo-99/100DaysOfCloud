# Day 13 - RDS & Amazon Aurora

## AWS RDS  (Relational Database Service)
- It’s a managed DB service for DB use SQL as a query language
- Multi AZ setup for DR (Disaster Recovery)
- Storage backed by EBS (gp2 or io1)
- Read replicas for improved read performance
- Continuous backups and restore to specific timestamp (Point in Time Restore)
- You **cannot** SSH into your RDS instance - because RDS is a managed service, we don't have access to the underlying EC2 instance

### RDS Backups
#### Automated backups:
- automatically enabled
- Daily full backup of the database
- Transaction logs are backed-up by RDS every 5 minutes
- => ability to restore to any point in time (from oldest backup to 5 minutes ago)
- 7 day retention by default, up to 35 days

#### DB Snapshots:
- Manually triggered by the user
- Retention of backup for as long as you want

####  RDS Read Replicas
- Up to 5 Read Replicas
- Within same AZ, Cross AZ or Cross Region
- Replication is ASYNC, so reads are eventually consistent
- Can be a Disaster Recovery solution if put in different AZ/region
    - One DNS name – automatic app failover to standby
    - SYNC replication
        
- Read replicas are used for SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)
- Putting read replica in **same AZ** helps **saving on your data transmission cost**
1
### RDS Security
#### At rest encryption
- Is done only when you first create the DB instance
- or: unencrypted DB => snapshot => copy snapshot as encrypted => create DB from snapshot
- Possibility to encrypt the master & read replicas with AWS KMS - AES-256 encryption
- Encryption has to be defined at launch time
- If the master is not encrypted, the read replicas cannot be encrypted
- Transparent Data Encryption (TDE) available for Oracle and SQL Server

#### In-flight encryption SSL Encryption
To enforce SSL:
- PostgreSQL: `rds.force_ssl=1` in the AWS RDS Console (Parameter Groups)
- MySQL: Run this SQL in the DB:
`GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL`
  

#### RDS Access Management 
- IAM policies help control who can manage AWS RDS (through the RDS API)
- Traditional Username and Password can be used to login into the database
- IAM-based authentication can be used to login into RDS MySQL & PostgreSQL

#### IAM Authentication
- Only available for **MySQL** & **PostgreSQL**
- You don’t need a password, just an **authentication token** obtained through IAM & RDS API calls
- Auth token's lifetime: **15 minutes**
  
EC2 instances with the right IAM role can make an API call to RDS Service to get Auth Token; Pass Auth Token to the database will make it log in.

- Benefits:
    - Network in/out must be encrypted using SSL
    - IAM to centrally manage users instead of DB
    - Can leverage IAM Roles and EC2 Instance profiles for easy integration
    
## Amazon Aurora
- Available for Postgres and MySQL
- claims 5x performance improvement over MySQL on RDS, over 3x the performance of Postgres on RDS
- Aurora storage automatically grows in increments of 10GB, up to 64 TB
- Up to have **15** replicas (while MySQL has 5)
- Failover in Aurora is instantaneous. It’s HA (High Availability) native.
- Aurora costs 20% more than RDS 
- Backtrack: restore data at any point of time without using backups

### High Availability and Read Scaling
- 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 need for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
- One Aurora Instance takes writes (master)
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross Region Replication

###Aurora DB Cluster
####Endpoints
1. One **Writer Endpoint**
   - pointing to master instance
2. One **Reader Endpoint**
    - Connection Load Balancing
    - Automatically connected to all read replicas
    - Load balancing happens at the connection level, not statement level
    - Expose one connection
    - Balanced to all the read replicas
    
### Aurora Security
- Similar to RDS because uses the same engines (MySQL or Postgres)
- You are responsible for protecting the instance with security groups
- You can’t SSH

## Aurora Serverless
- Good for infrequent, intermittent or unpredictable workloads
- Pay per second, can be more cost-effective
#### How it works
- In the backend, there are Aurora instances instantiated by AWS
- There will be a Proxy Fleet, managed by Aurora, which the clients can connect to.
- If the request increases, the Proxy Fleet gets connected with more Aurora instances.

## Global Aurora
### Aurora Cross Region Read Replicas:
- Useful for disaster recovery
- Up to 15 read replicas
### Aurora Global Database
- 1 Primary region (read/write)
- Up to 5 secondary regions (read-only), read replication lag is < 1 second
-Up to 16 read replicas per secondary region
- Promoting another region (for disaster recovery) has an RTO of < 1 minute