# Day 10 - Elastic Load Balancing

## Load Balancer Stickiness
- It is possible to implement stickiness so that the same client is always redirected to the same instance behind a load balancer

- This works for Classic Load Balancers & Application Load Balancers

- The “cookie” used for stickiness has an expiration date you control

- Use case: make sure the user doesn’t lose his session data

- Enabling stickiness may bring imbalance to the load over the backend EC2 instances

- If Application Load Balancer, the stickiness setting is going to be at the target group level
#### stickiness is very helpful if you want the same request originating from the same client, to go to the same target Ec2 instance

## Cross-Zone Load Balancing
- With Cross Zone Load Balancing: each load balancer instance distributes evenly across all registered instances in all AZ
- Otherwise, each load balancer node distributes requests evenly across the registered instances in its Availability Zone only.

1. CLB: disabled by default, no charge if enable
2. ALB: always on, cannot disable, no charge
3. NLB: disabled by default, get charged if enable

## SSL Certificates
### SSL/TLS - Basics
- An SSL Certificate allows traffic between your clients and your load balancer
  to be encrypted in transit (in-flight encryption)
  
- SSL refers to Secure Sockets Layer, used to encrypt connections
  
- TLS refers to Transport Layer Security, which is a newer version
  
- Nowadays, TLS certificates are mainly used, but people still refer as SSL


- Public SSL certificates are issued by Certificate Authorities (CA)
  
- Comodo, Symantec, GoDaddy, GlobalSign, Digicert, Letsencrypt, etc…


#### SSL certificates have an expiration date (you set) and must be renewed

![](Load%20Balancer%20-%20SSL%20Certificates.png)
1. HTTPS, because it's using SSL certificates and it's encrypted, it's secure, and it connects over the public internet to your load balancer.
2. And internally, your load balancer does something called SSL certificate termination.
3. In the backend, it can talk to your EC2 instance, using HTTP, so not encrypted, but the traffic goes over your VPC, which is private traffic network, and that is somewhat secure.


- The load balancer uses an X.509 certificate (SSL/TLS server certificate)
- You can manage certificates using ACM (AWS Certificate Manager)
- You can create upload your own certificates alternatively
- HTTPS listener:
  - You must specify a default certificate
  - You can add an optional list of certs to support multiple domains
  - Clients can use SNI (Server Name Indication) to specify the hostname they reach
  - Ability to specify a security policy to support older versions of SSL / TLS (legacy clients)
  
### SSL – Server Name Indication (SNI)
- SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites)
- It’s a “newer” protocol, and requires the client to indicate the hostname of the target server in the initial SSL handshake
- The server will then find the correct certificate, or return the default one

Note:
- Only works for **ALB & NLB** (newer generation), **CloudFront**
- Does not work for CLB (older gen)

### Warp Up - Elastic Load Balancers – SSL Certificates
##### Classic Load Balancer (v1)
- Support only one SSL certificate
- Must use multiple CLB for multiple hostname with multiple SSL certificates
#### Application Load Balancer & Network Load Balancer (v2)
- Supports multiple listeners with multiple SSL certificates
- Uses Server Name Indication (SNI) to make it work

## ELB – Connection Draining

- Feature naming:
  - CLB: Connection Draining
  - Target Group: Deregistration Delay (for ALB & NLB)

- Time to complete “in-flight requests” while
the instance is de-registering or unhealthy
- Stops sending new requests to the instance
which is de-registering
- Between 1 to 3600 seconds (1 hour), default is 300
seconds
- Can be disabled (set value to 0)
- Set to a low value if your requests are short

![](Connection%20Draining.png)
If EC2 instance is being terminated or is unhealthy, it is go into draining mode.

During the draining mode, the existing connections will be waited for the duration of the connection draining period to be completed.

By default that is 300 seconds. That means that any new connection that is made by the users into the ELB will be redirected to the other EC2 instances available and are being registered already registered to your ELB.

So your deregistration delay is going to be 300 seconds by default, but you can set it anywhere between one second to 3,600 seconds which is one hour.

And you can also disable it by setting the value to zero.

So if you have a web application that does very short request maybe one to five seconds, then you want to set your deregistration delay to something that's going to be quite low maybe 10, 20 seconds because you don't expect any connection any request to last any longer than 20 seconds.

But if your EC2 instances are very slow to respond, maybe to take minutes because they have a lot of data processing to do. Then you do want to set your connection draining something a bit higher to give a chance to these requests that are already in-flight to be completed.

If you don't want that behavior at all then you can just disable it set the value to zero and in case a connection is dropped while your EC2 instance is being killed, then users will retrieve an error.

And maybe it's the role of your users to just retry that request up until it succeeds by being redirected to a new EC2 instance okay.