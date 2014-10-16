## TLS on an Elastic Load Balancer

(**[Jump right to the ELB configuration choices.](#configuration-choices)**)

If you use an Amazon Elastic Load Balancer (ELB) as the public-facing endpoint for your project, you have limited control over your TLS configuration.

You **have control** over:

* certificate signing algorithm (e.g. SHA-256)
* private key algorithm and bit-length (e.g. RSA 4096-bit or ECDSA 256-bit)
* [HTTP Strict Transport Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) (if the header is set on backend instances)
* supported TLS protocol versions
* allowed ciphersuites

You **do not have control** over:

* [DH parameters](#limited-dh-parameters) (locked to 1024-bits, which is bad)
* [OCSP stapling](#ocsp-stapling)
* [SPDY and NPN support](#spdy-and-npn-support)
* [Secure client-initiated renegotiation](https://community.qualys.com/blogs/securitylabs/2011/10/31/tls-renegotiation-and-denial-of-service-attacks) (enabled, which may be bad)
* choice of elliptic curve algorithm (locked to 256-bits, which is fine)
* TLS TCP buffer sizes (haven't tested AWS' default)

Because of this, it's recommended that you terminate TLS on one or more EC2 instances directly. By doing so, you can use [18F's standard nginx configuration](nginx/ssl.rules), which addresses all of the above.

If you do use an ELB to terminate TLS, the next section discusses the downsides of ELBs in more detail, so that you know what you're giving up. Then, use the [ELB configuration choices](#configuration-choices) we've identified that provide the strongest possible current TLS configuration for an ELB.

### Downsides of ELBs

There are security, privacy, and performance issues with using ELBs to terminate TLS connections.

#### Limited DH parameters

Diffie-Hellman (DH) parameters are constants &mdash; large prime numbers &mdash; used to establish `ECDHE` and `DHE` ciphers for key exchange. They are typically generated once, of a certain size, and then pointed to in your TLS configuration forever more.

From the [SSL Labs Server Rating Guide](https://www.ssllabs.com/downloads/SSL_Server_Rating_Guide_2009e.pdf):

> Many servers that support DHE use DH parameters that provide 1024 bits of security. On such servers, the strength of the key exchange will never go above 1024 bits, even if the private key is stronger.

Amazon ELBs default to 1024-bit DH parameters, which reduces the security and privacy of our TLS connections.

nginx also defaults to 1024-bit, which is why 18F's nginx SSL configuration [points to a larger pre-generated dhparams file](https://github.com/18F/tls-standards/blob/master/nginx/ssl.rules#L42-L47).

See [this question on dhparams](https://security.stackexchange.com/questions/38206/can-someone-explain-a-little-better-what-exactly-is-accomplished-by-generation-o), [this article on DH ciphersuites](http://vincent.bernat.im/en/blog/2011-ssl-perfect-forward-secrecy.html), and [this post on dhparams in Apache](http://blog.ivanristic.com/2013/08/increasing-dhe-strength-on-apache.html) for more details.

#### OCSP Stapling

Amazon ELBs do not support [OCSP stapling](https://en.wikipedia.org/wiki/OCSP_stapling). OCSP stapling is a method to check whether the TLS certificate for a website has been revoked, at the very same time as you connect to the website. The revocation data is "stapled" to the TLS response at connection time.

Without OCSP stapling, browsers which do revocation checking (e.g. Firefox, but [not](https://www.imperialviolet.org/2011/03/18/revocation.html) [Chrome](https://www.imperialviolet.org/2014/04/19/revchecking.html)) have to hit a web API that TLS certificate authorities provide, that offer a list of revoked certificates. Doing this is slow, has poorer security properties, and is a privacy leak (as it shares browsing data with CAs).

OCSP stapling is not a new technology, but it's still not widely implemented. There are a couple of standards in the works that would make OCSP better and more useful:

* [OCSP Must-Staple](http://www.ietf.org/mail-archive/web/tls/current/msg10323.html) ([IETF](http://tools.ietf.org/html/draft-hallambaker-tlsfeature-02)) - Sort of like HSTS for OCSP, allows clients to hard-fail if the stapled response is invalid and not fall back to traditional OCSP retrieval.
* [OCSP Multi-Stapling](https://casecurity.org/2013/05/07/an-introduction-to-ocsp-multi-stapling/) ([IETF](http://datatracker.ietf.org/doc/rfc6961/)) - Support for stapling OCSP responses for intermediate certificates, not just client certificates.

#### SPDY and NPN support

Amazon ELBs do not enable SPDY or Next Protocol Negotiation (NPN).

**[SPDY](https://en.wikipedia.org/wiki/SPDY)** extends HTTP and deliver much better performance, especially for websites with many assets. SPDY allows efficient multiplexing and pipelining over a single TCP socket -- besides being overall faster, this also deprecates image spriting and domain sharding for delivering static assets.

SPDY was originally designed by Google and now used as the basis for the draft [HTTP/2](https://http2.github.io/faq/) spec. When HTTP/2 is finalized, 18F hopes to take immediate advantage of it.

**[NPN](https://technotes.googlecode.com/git/nextprotoneg.html)** is a way for servers and browsers to agree in advance on what protocols are supported, so that things like SPDY can be enabled opportunistically. NPN will eventually [being replaced by ALPN](https://www.imperialviolet.org/2013/03/20/alpn.html).

In Chrome, NPN support is required to turn on TLS False Start, which saves a TLS round trip and [makes connections faster](http://blog.chromium.org/2011/05/ssl-falsestart-performance-results.html). Firefox supports TLS False Start, but it's not clear under what conditions it's enabled.

The nginx SPDY plugin enables NPN automatically, so if you're using [18F's nginx configuration](nginx/ssl.rules) you'll get SPDY, NPN, and TLS False Start automatically.

### Configuration choices

Below is 18F's standard ELB configuration for TLS termination. This configuration:

* **Removes SSLv3 support.** [SSLv3 is insecure](http://googleonlinesecurity.blogspot.com/2014/10/this-poodle-bites-exploiting-ssl-30.html), and if it's supported at all, then an active attacker can force even users who are able to connect over TLS to downgrade to a SSLv3 connection. For this reason, we've disabled it entirely.
* **Supports robust forward secrecy.** Under this set of cipher choices, forward secrecy is enabled for nearly every browser you would expect to support. There is one non-forward-secret cipher choice, `RC4-SHA`, which is a carveout specifically for IE8 on Windows XP. All other browsers should choose a forward secret ciphersuite.

AWS comes with a few "predefined" configurations, but does not allow you to save your own predefined configurations. They must be set every time an ELB is created.

**CloudFormation users:** Use this **[sample ELB configuration](elb-cloudformation.json)** for CloudFormation.

##### Protocols enabled

- [ ] Protocol-SSLv2
- [X] Protocol-TLSv1
- [ ] Protocol-SSLv3
- [X] Protocol-TLSv1.1
- [X] Protocol-TLSv1.2

##### SSL Options

- [X] Server Order Preference

##### SSL Ciphers

- [X] ECDHE-ECDSA-AES128-GCM-SHA256
- [X] ECDHE-RSA-AES128-GCM-SHA256
- [X] ECDHE-ECDSA-AES128-SHA256
- [X] ECDHE-RSA-AES128-SHA256
- [X] ECDHE-ECDSA-AES128-SHA
- [X] ECDHE-RSA-AES128-SHA
- [X] ECDHE-ECDSA-AES256-GCM-SHA384
- [X] ECDHE-RSA-AES256-GCM-SHA384
- [X] ECDHE-ECDSA-AES256-SHA384
- [X] ECDHE-RSA-AES256-SHA384
- [X] ECDHE-RSA-AES256-SHA
- [X] ECDHE-ECDSA-AES256-SHA
- [ ] AES128-GCM-SHA256
- [ ] AES128-SHA256
- [ ] AES128-SHA
- [ ] AES256-GCM-SHA384
- [ ] AES256-SHA256
- [ ] AES256-SHA
- [X] DHE-RSA-AES128-SHA
- [X] DHE-DSS-AES128-SHA
- [ ] CAMELLIA128-SHA
- [ ] EDH-RSA-DES-CBC3-SHA
- [ ] DES-CBC3-SHA
- [X] ECDHE-RSA-RC4-SHA
- [X] RC4-SHA

... everything below this should stay unchecked ...
