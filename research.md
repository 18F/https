### OCSP Stapling

[OCSP stapling](https://en.wikipedia.org/wiki/OCSP_stapling) &mdash; a method of eliminating 3rd party cert revocation checking &mdash; is not currently enabled, but should be looked into, as it has [performance and privacy benefits](http://blog.cloudflare.com/ocsp-stapling-how-cloudflare-just-made-ssl-30).

We should also keep an eye on related standards as they advance -- [OCSP Must-Staple](http://www.ietf.org/mail-archive/web/tls/current/msg10323.html) ([IETF](http://tools.ietf.org/html/draft-hallambaker-tlsfeature-02)) and [OCSP Multi-Stapling](https://casecurity.org/2013/05/07/an-introduction-to-ocsp-multi-stapling/) ([IETF](http://datatracker.ietf.org/doc/rfc6961/)).

### IPv6

Amazon EC2, 18F's host, does not support IPv6 as publicly addressable IPs for normal instances. They are supported for Elastic Load Balancers (but even then, not inside of Virtual Private Clouds).

Amazon has said (as recently as [April 2014](https://forums.aws.amazon.com/thread.jspa?messageID=536049)) that IPv6 support is in the works for ordinary EC2 instances. Until that actually happens, we're not compiling in the IPv6 nginx module.

### SPDY and HTTP 2.0

We compile in the [SPDY](https://en.wikipedia.org/wiki/SPDY) module for nginx, in addition to the TLS module. SPDY is a protocol, originally designed by Google, to extend HTTP and deliver much better performance, especially for websites with many assets.

As of this writing, we're using nginx 1.6.0, with support for SPDY 3.1. SPDY is being used as the basis for work on [HTTP/2](http://http2.github.io/), which is still very much a draft, and not yet approaching finalization.

We'll update our version and configuration of nginx to accommodate future versions of SPDY (such as 4.0), and when the day comes we'll plan to accommodate HTTP/2.

### Elliptic Curve certificates

We'd like to use [elliptic curve certificates](https://blog.cloudflare.com/ecdsa-the-digital-signature-algorithm-of-a-better-internet). They are more performant, and are more likely to stand the test of time as RSA weakens.

Using ECDSA certificates removes support for Windows XP SP2 **and** SP3, cutting off Windows XP users entirely. Google and Cloudflare are early adopters of ECDSA certificates, but it looks like they have some sort of fallback certificate strategy.

There is a standard method to distinguish what signature algorithms a client supports, and to thus send different certs per-client, but this is only supported in TLS v1.2. It might be possible, then, to default to an RSA cert for all clients, but for TLS v1.2 users who advertise support for ECDSA, show an ECDSA cert.

ECDSA may be appropriate for projects that can acceptably remove support for Windows XP.

EC references:

* https://www.ssllabs.com/ssltest/clients.html
* https://serverfault.com/questions/585784/if-i-get-a-certificate-signed-for-ecdsa-will-older-browsers-be-able-to-use-rsa
* https://security.stackexchange.com/questions/43360/is-possible-that-a-tls-server-send-more-than-one-certificate-to-the-client-for-t
* https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography
* https://blog.cloudflare.com/ecdsa-the-digital-signature-algorithm-of-a-better-internet
* https://www.openssl.org/docs/apps/ecparam.html
* http://wiki.openssl.org/index.php/Command_Line_Elliptic_Curve_Operations
* https://www.tbs-certificates.co.uk/FAQ/en/generation_CSR_ECC_openssl.html
* http://tools.ietf.org/html/rfc5246#section-7.4.1.4.1
