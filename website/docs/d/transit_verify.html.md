---
layout: "vault"
page_title: "Vault: vault_transit_verify data source"
sidebar_current: "docs-vault-datasource-transit-verify"
description: |-
  Verify a signature using a Vault Transit signing key
---

# vault\_transit\_verify

This is a data source which can be used to verify a signature using a Vault Transit key.

## Example Usage

```hcl
data "vault_transit_verify" "test" {
  path        = "transit"
  key         = "test"
  signature   = "vault:v1:4kYRAVY/Q/6jjA3CT7HPhxKO+ru/4PhyGKBLRpn9DSeT99McPXEk302NXtzCzsvbSOZPif7f32tlr58iYoxjCQ=="
  input       = "aGVsbG8gd29ybGQ="
}
```

## Argument Reference

Each document configuration may have one or more `rule` blocks, which each accept the following arguments:

* `name` – (Required) Specifies the name of the encryption key that was used to generate the signature or HMAC.

* `path` - (Required) The path the transit secret backend is mounted at, with no leading or trailing `/`.

* `namespace` - (Optional) The namespace to provision the resource in.
  The value should not contain leading or trailing forward slashes.
  The `namespace` is always relative to the provider's configured [namespace](/docs/providers/vault/index.html#namespace).
  *Available only for Vault Enterprise*.

* `hash_algorithm` – (Optional) Specifies the hash algorithm to use. Currently-supported algorithms are:

	* `sha1`
	* `sha2-224`
	* `sha2-256`
	* `sha2-384`
	* `sha2-512`
	* `sha3-224`
	* `sha3-256`
	* `sha3-384`
	* `sha3-512`
	* `none`

* `input` – (Optional) Specifies the **base64 encoded** input data. One of
  `input` or `batch_input` must be supplied.

* `signature` – (Optional) Specifies the signature output from the
  `/transit/sign` function. One of the following arguments must be supplied
  `signature`, `hmac` or `cmac`.

* `hmac` – (Optional) Specifies the signature output from the
  `/transit/hmac` function. One of the following arguments must be supplied
  `signature`, `hmac` or `cmac`.

* `cmac` – (Optional) Specifies the signature output from the
  `/transit/cmac` function. One of the following arguments must be supplied
  `signature`, `hmac` or `cmac`.

* `reference` – (Optional)
  A user-supplied string that will be present in the `reference` field on the
  corresponding `batch_results` item in the response, to assist in understanding
  which result corresponds to a particular input. Only valid on batch requests
  when using ‘batch_input’ below.

* `batch_input` – (Optional) Specifies a list of items for processing.
  When this parameter is set, any supplied 'input', 'hmac' or 'signature' parameters
  will be ignored. 'batch_input' items should contain an 'input' parameter and
  either an 'hmac' or 'signature' parameter. All items in the batch must consistently
  supply either 'hmac' or 'signature' parameters. It is an error for some items to
  supply 'hmac' while others supply 'signature'. Responses are returned in the
  'batch_results' array component of the 'data' element of the response. Any batch
  output will preserve the order of the batch input. If the input data value of an
  item is invalid, the corresponding item in the 'batch_results' will have the key
  'error' with a value describing the error. The format for batch_input is:

  ```json
  {
    "batch_input": [
      {
        "input": "adba32==",
        "hmac": "vault:v1:1jFhRYWHiddSKgEFyVRpX8ieX7UU+748NBwHKecXE3hnGBoAxrfgoD5U0yAvji7b5X6V1fP"
      },
      {
        "input": "aGVsbG8gd29ybGQuCg==",
        "hmac": "vault:v1:/wsSP6iQ9ECO9RRkefKLXey9sDntzSjoiW0vBrWfUsYB0ISroyC6plUt/jN7gcOv9O+Ecow"
      }
    ]
  }
  ```

* `context` – (Optional) Base64 encoded context for key derivation.
  Required if key derivation is enabled; currently only available with ed25519
  keys.

* `signature_context` – (Optional) Base64 encoded context for Ed25519ctx and Ed25519ph signatures.

* `prehashed` – (Optional) Set to `true` when the input is already hashed.
  If the key type is `rsa-2048`, `rsa-3072` or `rsa-4096`, then the algorithm used to hash
  the input should be indicated by the `hash_algorithm` parameter. Just as the
  value to sign should be the base64-encoded representation of the exact binary
  data you want signed, when set, `input` is expected to be base64-encoded
  binary hashed data, not hex-formatted. (As an example, on the command line,
  you could generate a suitable input via `openssl dgst -sha256 -binary | base64`.)
  On Enterprise <EnterpriseAlert inline="true" />, enabling this along with
  hash_algorithm being set to `sha2-512` will activate Ed25519ph signatures for
  Ed25519 keys

* `signature_algorithm` – (Optional) When using a RSA key, specifies the RSA
  signature algorithm to use for signature verification. Supported signature types
  are:

	* `pss`
	* `pkcs1v15`

* `marshaling_algorithm` – (Optional) Specifies the way in which the signature was originally marshaled. This currently only applies to ECDSA keys. Supported types are:

	* `asn1`: The default, used by OpenSSL and X.509
	* `jws`: The version used by JWS (and thus for JWTs). Selecting this will
	  also expect the input encoding to URL-safe Base64 encoding instead of
	  standard Base64-encoding.

* `salt_length` – (Optional) The salt length used to sign. This currently only applies to the RSA PSS signature scheme. Options are:

	* `auto`: The default used by Golang (causing the salt to be as large as possible when signing)
	* `hash`: Causes the salt length to equal the length of the hash used in the signature
	- An integer between the minimum and the maximum permissible salt lengths for the given RSA key size.

## Attributes Reference

* `valid` - Returns `true` if the signature verification succeeded and `false` otherwise

* `batch_results` - The results returned from Vault if using `batch_input` 
