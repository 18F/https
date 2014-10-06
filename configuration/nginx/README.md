## Using this nginx configuration

The `ssl.rules` file in this directory is meant to be referenced as part of an nginx `server` block.

If your vhost is at `/etc/nginx/vhosts/site.conf`, the rules file should be placed at `/etc/nginx/ssl/ssl.rules`, near your certificate and private key.

For example, the TLS configuration for `https://18f.gsa.gov` [begins with](https://github.com/18F/18f.gsa.gov/blob/8e164eac888262629974cce49ee3df2311c0e5c6/deploy/18f-site.conf#L23-L25):

```nginx
ssl_certificate      /etc/nginx/ssl/keys/18f.crt;
ssl_certificate_key  /etc/nginx/ssl/keys/18f.key;
include ssl/ssl.rules;
```
