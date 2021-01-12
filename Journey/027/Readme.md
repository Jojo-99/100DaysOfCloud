# SAA Prepare - Day 14: AWS Security & Encryption: KMS, SSM Parameter Store, CloudHSM, Shield, WAF

## AWS Security & Encryption
### Encryption in flight (SSL)
- Data is encrypted before sending and decrypted after receiving
- SSL certificates help with encryption (HTTPS)
- Encryption in flight ensures no MITM (man in the middle attack) can happen

### Server side encryption at rest
- Data is encrypted after being received by the server. Data is decrypted before being sent
- It is stored in an encrypted form thanks to a key (usually a **data key**)
- The encryption / decryption keys must be managed somewhere and
the server must have access to it
  
### Client side encryption
- Data is encrypted by the client and never decrypted by the server
- Data will be decrypted by a receiving client
- The server should not be able to decrypt the data
- Could leverage Envelope Encryption

## AWS KMS (Key Management Service)
- Anytime you hear “encryption” for an AWS service, it’s most likely KMS
- Easy way to control access to your data, AWS manages keys for us
- Fully integrated with IAM for authorization
- But you can also use the CLI / SDK

### KMS – Customer Master Key (CMK) Types
#### Symmetric (AES-256 keys)
- First offering of KMS, **single encryption key** that is **used to Encrypt and Decrypt**
- AWS services that are integrated with KMS use Symmetric CMKs
- Necessary for envelope encryption
- You never get access to the Key unencrypted (must call KMS API to use)
#### Asymmetric (RSA & ECC key pairs)
- **Public (Encrypt)** and **Private Key (Decrypt)** pair
- Used for Encrypt/Decrypt, or Sign/Verify operations
- The public key is downloadable, but you can’t access the Private Key unencrypted
- Use case: encryption outside of AWS by users who can’t call the KMS API

### AWS KMS (Key Management Service)
- Able to fully manage the keys & policies: Create, Rotation policies, Disable, Enable
- Able to audit key usage (using CloudTrail)
- Three types of Customer Master Keys (CMK):
    - AWS Managed Service Default CMK: free
    - User Keys created in KMS: $1 / month
    - User Keys imported (must be 256-bit symmetric key): $1 / month
- + pay for API call to KMS ($0.03 / 10000 calls)

- Anytime you need to **share sensitive information**… use KMS
    - Database passwords
    - Credentials to external service
    - Private Key of SSL certificates
    
- The **value** in KMS is that the CMK used to encrypt data can never be retrieved by the user, and the CMK can be rotated for extra security

- Never ever store your secrets in plaintext, especially in your code
- Encrypted secrets can be stored in the code / environment variables
- KMS can only help in encrypting up to 4KB of data per call
- If **data > 4 KB**, use **envelope encryption**
- To give access to KMS to someone:
    - Make sure the Key Policy allows the user
    - Make sure the IAM Policy allows the API calls
    
### Copying Snapshots across regions
- KMS keys are linked to a specific region when you copy snapshots over they have to be re-encrypted
1. you would create a snapshot of your volume and any snapshot made from an encrypted volume is also encrypted with KMS and the same key
2. you need to copy that snapshot over to the new region, but you will specify a new KMS key to re-encrypt the data with. so became KMS Key B in the other region
3. finally, when you recreate a volume from that snapshot then that volume will be encrypted with a new KMS Key B

### KMS Key Policies
- Control access to KMS keys, “similar” to S3 bucket policies
- Difference: you cannot control access without them
- **Default KMS Key Policy**:
    - Created if you don’t provide a specific KMS Key Policy
    - Complete access to the key to the **root user** = entire AWS account
    - Gives access to the IAM policies to the KMS key
- **Custom KMS Key Policy**:
    - Define users, roles that can access the KMS key
    - Define who can administer the key
    - Useful for **cross-account access** of your KMS key
    
### Copying Snapshots across accounts
1. Create a Snapshot, encrypted with your own CMK
2. Attach a KMS Key Policy to authorize cross-account access
3. Share the encrypted snapshot
4. (in target) Create a copy of the Snapshot, encrypt it with a KMS Key in your account
5. Create a volume from the snapshot

## SSM Parameter Store
#### SSM Parameter Store has versioning and audit of values built-in directly
- Secure storage for configuration and **secrets**
- Optional Seamless Encryption using KMS
- **Serverless**, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Configuration management using path & IAM
- Notifications with CloudWatch Events
- Integration with CloudFormation

### Parameters Policies (for advanced parameters)
- Allow to assign a TTL to a parameter (expiration date) to force
updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time

## AWS Secrets Manager
- Newer service, meant for storing secrets
- Capability to force **rotation of secrets** every X days
- Automate generation of secrets on rotation (uses **Lambda**)
- Integration with **Amazon RDS** (MySQL, PostgreSQL, Aurora)
- Secrets are encrypted using KMS
- **Mostly meant for RDS integration**

## CloudHSM
- **KMS** => AWS manages the **software** for encryption
- **CloudHSM** => AWS provisions encryption **hardware**
- Dedicated Hardware (HSM = Hardware Security Module)
- You manage your own encryption keys entirely (not AWS)
- HSM device is tamper resistant, FIPS 140-2 Level 3 compliance
- **CloudHSM clusters are spread across Multi AZ (HA)** – must setup
- Supports both **symmetric and asymmetric encryption**(SSL/TLS keys)
- No free tier available
- Must use the CloudHSM Client Software
- **Redshift** supports CloudHSM for database encryption and key management
- Good option to use with **SSE-C** encryption

<img src="CloudHSM%20Diagram.png" alt="" width="1000px">


## AWS Shield
#### AWS Shield Standard:
- **Free** service that is activated for every AWS customer and it protects you from DDoS attacks
- Provides protection from attacks such as **SYN/UDP** Floods, Reflection attacks and other **layer 3/layer 4 attacks**

#### AWS Shield Advanced:
- Optional DDoS mitigation service ($3,000 per month per organization)
- Protect against more sophisticated attack on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS Global Accelerator, and Route 53
- 24/7 access to AWS DDoS response team (DRP)
- Protect against higher fees during usage spikes due to DDoS

## AWS WAF – Web Application Firewall
- Protects your web applications from common web exploits (Layer 7)
- **Layer 7 is HTTP** (vs Layer 4 is TCP)
- Deploy on **Application Load Balancer, API Gateway, CloudFront**

- Define Web **ACL** (Web Access Control List):
  - Rules can include: **IP addresses**, HTTP headers, HTTP body, or URI strings
  - Protects from common attack - **SQL injection** and **Cross-Site Scripting (XSS)**
  - Size constraints, **geo-match** (block countries)
  - **Rate-based rules** (to count occurrences of events) – for **DDoS protection**
  
#### AWS Firewall Manager

- Manage rules in all accounts of an AWS Organization
- Common set of security rules
- WAF rules (Application Load Balancer, API Gateways, CloudFront)
- AWS Shield Advanced (ALB, CLB, Elastic IP, CloudFront)
- Security Groups for EC2 and ENI resources in VPC

## AWS Shared Responsibility Model
#### AWS responsibility - Security of the Cloud
- Protecting infrastructure (hardware, software, facilities, and networking) that runs all of the AWS services
- Managed services like S3, DynamoDB, RDS etc
#### Customer responsibility - Security in the Cloud
- For EC2 instance, customer is responsible for management of the guest OS (including security patches and updates), firewall & network configuration, IAM etc

### for RDS
#### AWS responsibility:
- Manage the underlying EC2 instance, disable SSH access
- Automated DB patching
- Automated OS patching
- Audit the underlying instance and disks & guarantee it functions
#### Your responsibility:
- Check the ports / IP / security group inbound rules in DB’s SG
- In-database user creation and permissions
- Creating a database with or without public access
- Ensure parameter groups or DB is configured to only allow SSL connections
- Database encryption setting

### for S3
#### AWS responsibility:
- Guarantee you get unlimited storage
- Guarantee you get encryption
- Ensure separation of the data between different customers
- Ensure AWS employees can’t access your data
#### Your responsibility:
- Bucket configuration
- Bucket policy / public setting
- IAM user and roles
- Enabling encryption