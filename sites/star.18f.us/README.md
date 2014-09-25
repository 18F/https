### `*.18f.us`

This certificate was created for `*.18f.us`, to host a variety of staging and development domains that 18F uses internally.

It was created by Eric Mill on September 24, 2014.


### Installing the certificate for ELB use

To upload the certificate to IAM you need to use the [AWS CLI](http://aws.amazon.com/cli/)
and run the following command:
```
aws iam upload-server-certificate \
  --server-certificate-name star-18f-us \
  --certificate-body file://./star.18f.us.crt \
  --private-key file://./star.18f.us.key \
  --certificate-chain file://./star.18f.us-chain.crt
```

**NOTE**: Make sure to remote the client certificate from the chain leaving only
the intermediary ones.
