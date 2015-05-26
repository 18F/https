## Transport Layer Security (TLS) at 18F

18F is an all-TLS shop: all of our [websites](https://18f.gsa.gov/2014/11/13/why-we-use-https-in-every-gov-website-we-make/) and [APIs](https://github.com/18F/api-standards#always-use-https) enforce encryption.

We do this no matter how static or dynamic the content, and no matter how sensitive the service's information may appear to be. This stance is where the [federal government](https://18f.gsa.gov/2015/02/09/the-first-gov-domains-hardcoded-into-your-browser-as-all-https/) and the [whole internet](https://w3ctag.github.io/web-https/) are moving.

### Creating a new certificate

If you're an 18F employee and want a new TLS certificate, read our **[certificate creation process](certificates)**. We've recently switched to [SSLMate](https://sslmate.com/), so the process has changed.

We have a wildcard certificate for staging domains of the form `*.18f.us`, so you do not need a new certificate for those domains. (This only applies to third-level domains like `x.18f.us`. Fourth-level domains like `x.y.18f.us` cannot use this certificate.)

### Deploying and configuring certificates

We have a **[baseline nginx TLS configuration](https://github.com/fisma-ready/nginx)** for our EC2 instances that receive HTTP requests.

For sites that use an Elastic Load Balancer (ELB) to terminate TLS, we have a **[model TLS configuration for ELBs](configuration/elb.md)** and some analysis of the tradeoffs you face by using them.

### Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
