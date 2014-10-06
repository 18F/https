## Configuring SSL/TLS

There are 3 general ways you might terminate a SSL/TLS connection here, each of which will be configured differently:

* An [EC2 instance](#terminating-on-an-ec2-instance) directly.
* An [Elastic Load Balancer](#terminating-on-an-elastic-load-balancer).
* A [content delivery network](#terminating-on-a-cdn), like CloudFront.

### Terminating on an EC2 instance

This affords the greatest control over TLS termination, and we have a **[pretty good nginx TLS configuration](nginx/ssl.rules)** that you should use.

You can use Elastic IP addresses to reserve a static IP address that can be assigned to a particular instance, and re-assigned at any time. This IP address is safe to enter into DNS records, making it safe to point a domain name directly to a particular instance.

### Terminating on an Elastic Load Balancer

ELBs have limited TLS configuration options. Because of this, there are security, privacy, and performance issues with using ELBs to terminate TLS connections.

**[Read our ELB guidance](elb.md)** to find our standard configuration options, and to understand the downsides.

Even if you do use an ELB, the ELB should communicate to its backend instances over TLS, so you should still use 18F's [nginx configuration](nginx/ssl.rules) on those instances.

### Terminating on a CDN

Generally speaking, you won't have any control over TLS termination on a CDN. Depending on the CDN, and the service level provided, you might be able to determine the certificate.

If an 18F team member feels like their application should have some or all of its components behind a CDN, discuss with the DevOps team before proceeding.
