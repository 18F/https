### `myra.treasury.gov`

This certificate was created for `myra.treasury.gov`, to host the live site for [MyRA](https://myra.treasury.gov).

It was created by Eric Mill, through the Department of Treasury's Entrust account, on October 9, 2014.

#### Entrust chain

This certificate chain, located at [`myra.treasury.gov-chain.crt`](myra.treasury.gov-chain.crt), uses 2 intermediates and a client cert:

* [`myra.treasury.gov.crt`](myra.treasury.gov.crt)
* [`entrust-intermediate-2.crt`](entrust-intermediate-2.crt)
* [`entrust-intermediate-1.crt`](entrust-intermediate-1.crt)

There's also:

* [`entrust-intermediate-chain.crt`](entrust-intermediate-chain.crt) - Just the intermediate chain, suitable for use in an AWS ELB.
* [`myra.treasury.gov-chain.crt`](myra.treasury.gov-chain.crt) - The full client + intermediates chain, suitable for use in nginx.
* [`myra.treasury.gov.csr`](myra.treasury.gov.csr) - The original CSR used to create the certificate.

#### Provisional certificate

This is a provisional SSL certificate, that expires in 2 months, on **December 9, 2014**.

18F plans to issue a longer-lived SSL certificate through its own provider before **December 9, 2014**.
