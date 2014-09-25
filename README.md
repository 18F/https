## SSL at 18F

18F is an all-SSL shop: all of our [websites](https://18f.gsa.gov/) and [APIs](https://github.com/18F/api-standards#always-use-https) enforce encryption. We do this no matter how static or dynamic the content, and no matter how sensitive the service's information may appear to be.

This repository contains our:

* **standards and practices**, e.g. key generation, SSL/nginx/CDN configuration
* **research and knowledge base** on deploying SSL on the web today
* **discussion and collaboration** with people inside and outside of the government

### Creating a new certificate

If you're an 18F employee and want a new SSL certificate, read the **[certificate creation process](https://github.com/18F/ssl-standards/blob/master/certificates/creating.md)**.

We have a wildcard certificate for staging domains of the form `*.18f.us`, so you do not need a new certificate for those domains. (This only applies to third-level domains like `x.18f.us`. Fourth-level domains like `x.y.18f.us` cannot use this certificate.)

### Publishing our certificates

We store our SSL certificates, certificate requests, and some accompanying metadata in the [`sites/`](sites) directory. Accompanying private keys are, of course, not in this repository.

### Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
