# Day 2 - Security Groups, IPs

### Security Groups
- They control how the traffic is allowed into or out of EC2 Machines
- You can specify allow rules but you can't create deny rules. By default, security groups allow all outbound traffic. 
![](Security%20Group%20Diagram.png)
  
- Can be attached to multiple instances
- Locked down to a region / VPC combination
- Dose live "outside" the EC2: it is not like the application in the EC2, it's really a firewall outside the EC2 instance.
- It's good to maintain one separate security group for SSH access
- If your application is not accessible (time out), then it’s a security group issue
- If your application gives a “connection refused“ error, then it’s an application
  error or it’s not launched
- All **inbound** traffic is **blocked** by default    
- All **outbound** traffic is **authorised** by default
![](Mulitple%20SG.png)
  - If you have multiple security groups associated with an instance, then the rules are aggregated.
  
### Private vs Public Ip (IPv4)
- Networking has two sorts of IPs. IPv4 and IPv6:
  - IPv4: 1.160.10.240
  - IPv6: 3ffe:1900:4545:3:200:f8ff:fe21:67cf
  
- IPv4 is still the most common format used online.
- IPv6 is newer and solves problems for the Internet of Things (IoT).
- IPv4 allows for 3.7 billion different addresses in the public space
- IPv4: [0-255].[0-255].[0-255].[0-255].

## Private vs Public IP (IPv4) Fundamental Differences
- Public IP:
  - Public IP means the machine can be identified on the internet (WWW)
  - Must be **unique** across the **whole web** (not two machines can have the same public IP).
  - Can be geo-located easily
  
- Private IP:
  - Private IP means the machine can only be identified on a private network only
  - The IP must be unique across the private network
  - BUT two different private networks (two companies) can have the same IPs.
  - Machines connect to WWW using a NAT + internet gateway (a proxy)
  - Only a specified range of IPs can be used as private IP
  
## Elastic IPs
- When you stop and then start an EC2 instance, it can **change** its public
IP.
- If you need to have a fixed public IP for your instance, you need an
Elastic IP
- An Elastic IP is a public **IPv4** IP you own as long as you don’t delete it
- You can attach it to **one instance** at a time 
- With an Elastic IP address, you can mask the failure of an instance or software
  by rapidly remapping the address to another instance in your account.
- You can only have **5 Elastic IP** in your account (you can ask AWS to increase
  that).
- Overall, ***try to avoid using Elastic IP***:
  - They often reflect poor architectural decisions
  - Instead, use a random public IP and register a DNS name to it
  - Or, as we’ll see later, use a Load Balancer and don’t use a public IP