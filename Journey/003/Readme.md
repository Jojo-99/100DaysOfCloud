# Day 3 - EC2 User Data

## EC2 User Data
- It is possible to bootstrap our instances using an EC2 User data script.
- bootstrapping means launching commands when a machine starts
- That script is only run once at the instance first start
- EC2 user data is used to automate boot tasks such as:
    - Installing updates
    - Installing software
    - Downloading common files from the internet
- Anything you can think of
- The EC2 User Data Script runs with the root user

####  * Remember, EC2 User Date is automatically with the sudo command

#### You are getting a permission error exception when trying to SSH into your Linux Instance
The key is missing perimissions chmod 0400
