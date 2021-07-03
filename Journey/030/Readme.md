# Review CNA - Note Taking Serverless Application - Day 1
## Why need serveless?
Traditionally, we’ve built and deployed web applications where we have some degree of control over the HTTP requests that are made to our server. Our application runs on that server and we are **responsible for provisioning and managing the resources** for it. There are a few issues with this.

1. We are charged for **keeping the server up** even when we are not serving out any requests. 
2. We are **responsible for uptime and maintenance** of the server and all its resources. 
3. We are also responsible for applying the appropriate **security updates** to the server. 
4. As our usage scales we need to manage **scaling up our server** as well. And as a result manage scaling it down when we don’t have as much usage. 

## Serverless computing
**Serverless computing** (or serverless for short), is an execution model where the cloud provider (AWS, Azure, or Google Cloud) is **responsible for executing a piece of code by dynamically allocating the resources**. And only charging for the amount of resources used to run the code. The code is typically run inside **stateless containers** that can be triggered by a variety of events including http requests, database events, queuing services, monitoring alerts, file uploads, scheduled events (cron jobs), etc. The code that is sent to the cloud provider for execution is usually **in the form of a *function***. **Hence serverless is sometimes referred to as “Functions as a Service” or “FaaS”.**

## Advantages of serverless computing
- No server management is necessary
- Developers are only charged for the server space they use, reducing cost
- Serverless architectures are inherently scalable
- Quick deployments and updates are possible
- Code can run closer to the end user, decreasing latency

## Disadvantages of serverless computing
- Testing and debugging become more challenging
- Serverless computing introduces new security concerns
- Serverless architectures are not built for long-running processes
- Performance may be affected
- Vendor lock-in is a risk

## Serverless vs Microservices
Microservices is a way to design an application, while serverless is a way to run an application (or a part of an application). ... Not only are they both common technologies within cloud-based environments, but serverless functions are one way to host microservices.
