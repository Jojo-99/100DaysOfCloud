# Day 13 - 

## AWS RDS  (Relational Database Service)
- It’s a managed DB service for DB use SQL as a query language
- Multi AZ setup for DR (Disaster Recovery)
- Storage backed by EBS (gp2 or io1)
- Read replicas for improved read performance
- Continuous backups and restore to specific timestamp (Point in Time Restore)
- You cannot SSH into your RDS instance - because RDS is a managed service, we don't have access to the underlying EC2 instance

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
        - 
- Read replicas are used for SELECT (=read) only kind of statements (not INSERT, UPDATE, DELETE)
- Putting read replica in **same AZ** helps **saving on your data transmission cost**