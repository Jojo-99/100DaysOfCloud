# Solutions Architect Associate Prepare - Day 1

## EC2 Spot Instance 
More details go back to Day 3
- The hourly spot price varies based on offer and capacity
- If the current spot price > your max price you can choose to stop or terminate your
  instance with a 2 minutes grace period.
  
**Spot  Block**: “block” spot instance during a specified time frame (1 to 6 hours) without interruptions

### How to terminate Spot Instances?
- You can only cancel Spot Instance requests that are **open**, **active**, or **disabled**
- **Cancelling a Spot Request does not terminate instances**
  1. First, cancel a Spot Request
  2. then terminate the associated Spot Instances

#### Spot Request
- Includes: Maximum price, Desired number of instances, launch specification (AMI...), request type, valid from, valid until
  
- Two types for post instances: 
  - one-time
    1. as soon as spot request is fulfilled, instances are going to be launched.
    2. then spot request will be go away / invalid
  - persistent