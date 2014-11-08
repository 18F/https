### `pay.donate.peacecorps-dev.18f.us`

This certificate is used for encrypting the connection between pay.gov's QA
servers and our demo site. The cert is needed (instead of using `*.18f.us`)
because we wanted to logically separate all payment access (i.e. information
needed for the payment processor) from the rest of the donations site (which
is publicly accessible).

It was created by Sean Herron on 2014-10-24.
