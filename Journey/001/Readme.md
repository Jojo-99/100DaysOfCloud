# Day 1

[Udemy - Ultimate AWS Certified Developer Associate 2020](https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/learn/lecture/11851550#overview)

## AWS Fundamentals

### AWS Regions
- A region is a cluster of data centers
- Most AWS services are region-scoped


### AWS Availability Zones
- Each region has many availability zones (usually 3, min is 2, max is 6).
- Each availability zone (AZ) is one or more discrete data centers with redundant power, networking, and connectivity
- They’re separate from each other, so that they’re isolated from disasters
- They’re connected with high bandwidth, ultra-low latency networking

### IAM
![IAM](IAM.png)
- One IAM User per **PHYSICAL** PERSON
- One IAM Role per **Application**
- IAM credentials should NEVER BE SHARED
- Never, ever, ever, ever, write IAM credentials in code. EVER.
- Never use the ROOT account except for initial setup.
- Never use ROOT IAM Credentials

### EC2
- Renting virtual machines (EC2)
- Storing data on virtual drives (EBS)
- Distributing load across machines (ELB)
- Scaling the services using an auto-scaling group (ASG)

![SSH-Summary-Table](SSH-Summary-Table.png)

*Ps*:  
- Putty is a drop-in replacement for SSH
- EC2 Instance Connect allows you to connect to your EC2 Instance directly from your browser on any operating system.

