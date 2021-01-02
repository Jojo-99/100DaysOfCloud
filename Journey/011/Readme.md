# Day 11 - Auto Scaling Group Overview

### ASG Overview
- Scaling policies can be on CPU, Network… and can even be on custom metrics or based on a schedule (if you know your visitors patterns)
- ASGs use Launch configurations or Launch Templates (newer)
- To update an ASG, you must provide a new launch configuration / launch template
- IAM roles attached to an ASG will get assigned to EC2 instances
- ASG are free. You pay for the underlying resources being launched
- Having instances under an ASG means that if they get terminated for whatever reason, the ASG will **automatically create new ones as a replacement**. Extra safety!
- ASG can terminate instances marked as unhealthy by an LB (and hence replace them)

### ASGs have the following attributes
- A launch configuration
    - AMI + Instance Type
    - EC2 User Data
    - EBS Volumes
    - Security Groups
    - SSH Key Pair
- Min Size / Max Size / Initial Capacity
- Network + Subnets Information
- Load Balancer Information
- Scaling Policies

### Auto Scaling Alarms
- It is possible to scale an ASG based on **CloudWatch alarms**
- An Alarm monitors a metric (such as Average CPU)
- *Metrics are computed for the overall ASG instances*
- Based on the alarm:
- We can create scale-out policies (increase the number of instances)
    - We can create scale-in policies (decrease the number of instances)
### Auto Scaling New Rules
It is now possible to define ”better” auto scaling rules that are directly
managed by EC2
- Target Average CPU Usage
- Number of requests on the ELB per instance
- Average Network In
- Average Network Out
  

- These rules are easier to set up and can make more sense

### Auto Scaling Custom Metric
- We can auto scale based on a custom metric (ex: number of connected
users)
 1. Send custom metric from application on EC2 to CloudWatch
(PutMetric API)
 2. Create CloudWatch alarm to react to low / high values
 3. Use the CloudWatch alarm as the scaling policy for ASG



### Scaling Policies
1. Target Tracking Scaling
    - Most simple and easy to set-up
    - Example: I want the average ASG CPU to stay at around 40%
  
2. Simple / Step Scaling

- Have a lot of control about the scale out events, when units of capacity are added, and the scale in events where units of capacity are removed.
  - Example:
    - When a CloudWatch alarm is triggered (example CPU > 70%), then add 2 units
    - When a CloudWatch alarm is triggered (example CPU < 30%), then remove 1
3. Scheduled Actions
    - Anticipate a scaling based on known usage patterns
    - Example: increase the min capacity to 10 at 5 pm on Fridays
    
### Scaling Cooldowns

A scaling cooldown helps you prevent your Auto Scaling group from launching or terminating additional instances before the effects of previous activities are visible.

- The default cooldown for Auto Scaling group, we can create cooldowns
  that apply to a specific simple scaling policy
  
- A scaling-specific cooldown period overrides the default cooldown period.

- One common use for scaling-specific cooldowns is with a scale-in policy—a policy that terminates instances based on a specific criteria or metric. Because this policy terminates instances, Amazon EC2 Auto Scaling needs less time to determine whether to terminate additional instances.

- Default cooldown period: 300 seconds, can reduce costs by applying a  scaling-specific cooldown period of 180 seconds to the scale-in policy

- If your application is scaling up and down multiple times each hour, modify the Auto Scaling Groups cool-down timers and the CloudWatch Alarm Period that triggers the scale in