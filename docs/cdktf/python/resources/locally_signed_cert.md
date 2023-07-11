---
page_title: "tls_locally_signed_cert Resource - terraform-provider-tls"
subcategory: ""
description: |-
  Creates a TLS certificate in PEM (RFC 1421) https://datatracker.ietf.org/doc/html/rfc1421 format using a Certificate Signing Request (CSR) and signs it with a provided (local) Certificate Authority (CA).
---


<!-- Please do not edit this file, it is generated. -->
# tls_locally_signed_cert (Resource)

Creates a TLS certificate in [PEM (RFC 1421)](https://datatracker.ietf.org/doc/html/rfc1421) format using a Certificate Signing Request (CSR) and signs it with a provided (local) Certificate Authority (CA).

-> **Note** Locally-signed certificates are generally only trusted by client software when
setup to use the provided CA. They are normally used in development environments
or when deployed internally to an organization.

## Example Usage

```python
# Code generated by 'cdktf convert' - Please report bugs at https://cdk.tf/bug
from constructs import Construct
from cdktf import Fn, Token, TerraformStack
#
# Provider bindings are generated by running `cdktf get`.
# See https://cdk.tf/provider-generation for more details.
#
from imports.tls.locally_signed_cert import LocallySignedCert
class MyConvertedCode(TerraformStack):
    def __init__(self, scope, name):
        super().__init__(scope, name)
        LocallySignedCert(self, "example",
            allowed_uses=["key_encipherment", "digital_signature", "server_auth"],
            ca_cert_pem=Token.as_string(Fn.file("ca_cert.pem")),
            ca_private_key_pem=Token.as_string(Fn.file("ca_private_key.pem")),
            cert_request_pem=Token.as_string(Fn.file("cert_request.pem")),
            validity_period_hours=12
        )
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `allowed_uses` (List of String) List of key usages allowed for the issued certificate. Values are defined in [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280) and combine flags defined by both [Key Usages](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.3) and [Extended Key Usages](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.12). Accepted values: `any_extended`, `cert_signing`, `client_auth`, `code_signing`, `content_commitment`, `crl_signing`, `data_encipherment`, `decipher_only`, `digital_signature`, `email_protection`, `encipher_only`, `ipsec_end_system`, `ipsec_tunnel`, `ipsec_user`, `key_agreement`, `key_encipherment`, `microsoft_commercial_code_signing`, `microsoft_kernel_code_signing`, `microsoft_server_gated_crypto`, `netscape_server_gated_crypto`, `ocsp_signing`, `server_auth`, `timestamping`.
- `ca_cert_pem` (String) Certificate data of the Certificate Authority (CA) in [PEM (RFC 1421)](https://datatracker.ietf.org/doc/html/rfc1421) format.
- `ca_private_key_pem` (String, Sensitive) Private key of the Certificate Authority (CA) used to sign the certificate, in [PEM (RFC 1421)](https://datatracker.ietf.org/doc/html/rfc1421) format.
- `cert_request_pem` (String) Certificate request data in [PEM (RFC 1421)](https://datatracker.ietf.org/doc/html/rfc1421) format.
- `validity_period_hours` (Number) Number of hours, after initial issuing, that the certificate will remain valid for.

### Optional

- `early_renewal_hours` (Number) The resource will consider the certificate to have expired the given number of hours before its actual expiry time. This can be useful to deploy an updated certificate in advance of the expiration of the current certificate. However, the old certificate remains valid until its true expiration time, since this resource does not (and cannot) support certificate revocation. Also, this advance update can only be performed should the Terraform configuration be applied during the early renewal period. (default: `0`)
- `is_ca_certificate` (Boolean) Is the generated certificate representing a Certificate Authority (CA) (default: `false`).
- `set_subject_key_id` (Boolean) Should the generated certificate include a [subject key identifier](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.1.2) (default: `false`).

### Read-Only

- `ca_key_algorithm` (String) Name of the algorithm used when generating the private key provided in `ca_private_key_pem`.
- `cert_pem` (String) Certificate data in [PEM (RFC 1421)](https://datatracker.ietf.org/doc/html/rfc1421) format. **NOTE**: the [underlying](https://pkg.go.dev/encoding/pem#Encode) [libraries](https://pkg.go.dev/golang.org/x/crypto/ssh#MarshalAuthorizedKey) that generate this value append a `\n` at the end of the PEM. In case this disrupts your use case, we recommend using [`trimspace()`](https://www.terraform.io/language/functions/trimspace).
- `id` (String) Unique identifier for this resource: the certificate serial number.
- `ready_for_renewal` (Boolean) Is the certificate either expired (i.e. beyond the `validity_period_hours`) or ready for an early renewal (i.e. within the `early_renewal_hours`)?
- `validity_end_time` (String) The time until which the certificate is invalid, expressed as an [RFC3339](https://tools.ietf.org/html/rfc3339) timestamp.
- `validity_start_time` (String) The time after which the certificate is valid, expressed as an [RFC3339](https://tools.ietf.org/html/rfc3339) timestamp.

## Automatic Renewal

This resource considers its instances to have been deleted after either their validity
periods ends (i.e. beyond the `validity_period_hours`)
or the early renewal period is reached (i.e. within the `early_renewal_hours`):
when this happens, the `ready_for_renewal` attribute will be `true`.
At this time, applying the Terraform configuration will cause a new certificate to be
generated for the instance.

Therefore in a development environment with frequent deployments it may be convenient
to set a relatively-short expiration time and use early renewal to automatically provision
a new certificate when the current one is about to expire.

The creation of a new certificate may of course cause dependent resources to be updated
or replaced, depending on the lifecycle rules applying to those resources.

<!-- cache-key: cdktf-0.17.1 input-68f5a3ac6e15730702eda898fd0b26d83e0623e2680b4782c44d4d8fba2cfe8f -->