### Site certificate data

18F keeps its SSL certificates and certificate requests here, with a tiny bit of documentation on how they got created.

Each site directory should have:

* The certificate request (`.csr`) file.
* The certificate (`.crt`) for the domain.
* The full certificate chain (`.crt`) for the domain and its intermediates.
* A `README.md` containing why the cert was bought, and who purchased it when.

#### Intermediate certificates

Since 18F currently uses [Namecheap's domain-validated PositiveSSL certificates](https://www.namecheap.com/security/ssl-certificates/domain-validation.aspx), the intermediate certificates will be the same every time. They are both included in this directory:

* `COMODORSADomainValidationSecureServerCA.crt` - Intermediate #1: directly signs our client certificates, chains to Intermediate #2.
* `COMODORSAAddTrustCA.crt` - Intermediate #2: signs Intermediate #1, chains to root certificate.

The root certificate is an AddTrust CA root, but we should not include this in our certificate chains, as browsers will not use it during certificate chain verification. (Browsers will look in their trusted root store instead.)
