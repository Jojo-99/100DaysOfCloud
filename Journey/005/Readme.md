# Day 5 - Wrap up : IAM + EC2 

## EC2 Pricing
- You are billed by the second, with a minimum of 60 seconds.
- You also pay for other factors such as storage, data transfer, fixed IP
  public addresses, load balancing   
- You do not pay for the instance if the instance is stopped

The best way to know the pricing is to consult the pricing page:
[Pricing page](https://aws.amazon.com/ec2/pricing/on-demand/)

## AMI

- What’s an AMI – an image to use to create our instances
- AMIs can be built for Linux or Windows machines

#### Why would you use a custom AMI?
- Using a custom built AMI can provide the following advantages:
- Pre-installed packages needed
- Faster boot time (no need for long ec2 user data at boot time)
- Machine comes configured with monitoring / enterprise software
- Security concerns – control over the machines in the network
- Control of maintenance and updates of AMIs over time
- Active Directory Integration out of the box
- Installing your app ahead of time (for faster deploys when auto-scaling)
- Using someone else’s AMI that is optimised for running an app, DB, etc…
- **AMI are built for a specific AWS region (!)** ***Not Globally available***

## EC2 – Checklist
- Know how to SSH into EC2 (and change .pem file permissions)
- Know how to properly use security groups
- Know the fundamental differences between private vs public vs elastic IP
- Know how to use User Data to customize your instance at boot time
- Know that you can build custom AMI to enhance your OS
- EC2 instances are billed by the second