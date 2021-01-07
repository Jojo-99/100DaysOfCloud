# SAA Prepare - Day 4: Route53 routing policies & Health Checks

## Route53 routing policies
### Simple Routing Policy
- Maps a hostname to another hostname
- Use when you need to redirect to a single resource
- You can’t attach health checks to simple routing policy
- If multiple values are returned, a random one is chosen by the client - client load balancing

### Weighted Routing Policy
- Control the % of the requests that go to specific endpoint
- Helpful to test 1% of traffic on new app version for example
- Helpful to split traffic between two regions
- Can be associated with Health Checks

### Latency Routing Policy
- Redirect to the server that has the least latency close to us
- Latency is evaluated in terms of user to designated AWS Region

### Failover Routing Policy
- oute 53 will have a health check pointing to the primary, associated with the primary record. In case that health check fails, automatically, Route 53 will failover to the secondary instance when there is a DNS query.

### Geo Location Routing Policy
- **based on user location**
- Can specify traffic from specify countries go to specific IP 
- Should create a “default” policy for that non-specify countries (in case there’s no match on
  location)
  
### Multi Value Routing Policy
- Use when routing traffic to **multiple resources**
- Want to associate a Route 53 health checks with records
- Up to 8 healthy records are returned for each Multi Value query
- Multi Value is not a substitute for having an ELB, they are different

## Route 53 Health Checks
- Have X health checks failed => unhealthy (default 3)
- After X health checks passed => health (default 3)
- Default Health Check Interval: 30s (can set to 10s – **higher cost**)
    - About 15 health checkers will check the endpoint health (30s)
    - => one request every 2 seconds on average
- Can have HTTP, TCP and HTTPS health checks (no SSL verification)
- Possibility of integrating the health check with CloudWatch
- Health checks can be linked to Route53 DNS queries

### Route53 as a Registrar
- A **domain name registrar** is an organization that manages the
reservation of Internet domain names

### 3rd Party Registrar with AWS Route 53
- If you buy your domain on 3rd party website, you can still use Route53.
    1. Create a Hosted Zone in Route 53
    2. Update NS (name server) Records on 3rd party website to use Route 53 name servers
- Domain Registrar != DNS
- (But each domain registrar usually comes with some DNS features)