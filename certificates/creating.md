### So you want a new SSL certificate

This is 18F's documentation for their SSL certificate creation, purchase, and installation process. The order of operations is basically:

* Generate a 4096-bit private key.
* Back up that key immediately please!
* Generate a certificate request for the desired domain.
* Purchase a new certificate.
* Create a full certificate chain.
* Install the certificate and key.
* Publish the cert and CSR in a public repository.

All newly purchased 18F SSL certificates should go through this process.

#### Generating a private key

The first step to a new certificate is always to create a new private key. **Every certificate should have its own unique associated private key.**

We use [RSA(https://en.wikipedia.org/wiki/RSA_(cryptosystem)) keys with a **4096-bit key length** (2048 is acceptable, but 4096 preferred).

Create an encrypted key with the following command. You will be asked to choose a passphrase which you will use any time you wish to decrypt the key.

```bash
openssl genrsa -aes256 -out your-site-encrypted.key 4096
```

Decrypt the key. You will use the passphrase you picked in the previous command.

```bash
openssl rsa -in your-site-encrypted.key -out your-site.key
```

#### Backing up the private key

[ TBD ]

#### Creating the certificate request

Generate the certificate request, signed with SHA-256. You will be asked for details, explained below.

```bash
openssl req -new -sha256 -key your-site.key -out your-site.csr
```

You will be prompted to enter details for the CSR. You will need to fill in the country (US), the State (District of Columbia), the locality (Washington), the organization name (General Services Administration), the unit name (18F), and a blank email.

You will also need to enter a Common Name, which should be the domain name you are getting the certificate issued for. Examples might be `18f.gsa.gov` or `*.18f.us`.

Here's the CSR we used for `https://18f.gsa.gov`:

```
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:District of Columbia
Locality Name (eg, city) []:Washington
Organization Name (eg, company) [Internet Widgits Pty Ltd]:General Services Administration
Organizational Unit Name (eg, section) []:18F
Common Name (e.g. server FQDN or YOUR name) []:18f.gsa.gov
Email Address []:
```

This will create a `.csr` file that is suitable for submitting to the CA you are using to issue the certificate.

#### Purchasing the certificate

Currently, 18F is using [Namecheap](https://www.namecheap.com/security/ssl-certificates/domain-validation.aspx)'s domain validation certificates for our subdomain and wildcard certificates.

You will need to speak with the 18F DevOps team to confirm the certificate you need, and the 18F Ops team to get purchase approval.

Certificates are purchased _before_ choosing the domain name or submitting the certificate request, so it is possible to purchase certificates before creating the certificate request.

#### Actually getting the certificate

These steps require a teammate with login access to the 18F Namecheap account. The below screenshots show the steps as captured during the creation of our `*.18f.us` wildcard certificate.

Visit the [Namecheap SSL certificates list](https://manage.www.namecheap.com/myaccount/ssl-list.asp). You will see a list of already-created certificates, and any purchased-but-not-yet-created certificates. The below screenshot shows one of each:

![0-your-ssl](images/0-your-ssl.png)

To create the certificate, click the "Activate Now" link. On the next page (shown below) select "nginx" for a web server, paste in the contents of the `.csr` file you created earlier, and click "Next".

![1-csr-input](images/1-csr-input.png)

Verify that the certificate request information is what you expected, and especially that the "Common Name" field shows the correct domain.

We do not currently have any `@18f.us` email addresses set up. You will need to send the approver email to one of the email addresses listed on the `18f.us` WHOIS records. In the screenshot below, this is a particular staff member. For future requests, this will include 18F's public contact alias.

![2-choose-email](images/2-choose-email.png)

On the next page, you will choose the email address to receive the certificate. **Change this email address to be 18F's public contact address**, as shown below.

![3-delivery-details](images/3-delivery-details.png)

After that screen, you should be done, and you should see the lovely flowchart below.

![4-done](images/4-done.png)

#### Creating the certificate chain

[ WIP ]

#### Installing the certificate and private key

[ WIP ]

#### Documenting the certificate and CSR

[ TBD ]

#### Resources

* https://konklone.com/post/switch-to-https-now-for-free
* https://bettercrypto.org/static/applied-crypto-hardening.pdf
* http://support.f5.com/kb/en-us/solutions/public/11000/400/sol11440.html
