---
layout: "vault"
page_title: "Vault: vault_pki_secret_backend_config_cmpv2 data source"
sidebar_current: "docs-vault-datasource-pki-secret-backend-config-cmpv2"
description: |-
 Reads the PKI CMPv2 configuration from Vault Enterprise. 
---

# vault\_pki\_secret\_backend\_config\_cmp

Reads the PKI CMPv2 configuration from Vault Enterprise.

~> **Important** All data retrieved from Vault will be
written in cleartext to state file generated by Terraform, will appear in
the console output when Terraform runs, and may be included in plan files
if secrets are interpolated into any resource attributes.
Protect these artifacts accordingly. See
[the main provider documentation](../index.html)
for more details.

## Example Usage

```hcl
resource "vault_mount" "pki" {
  path        = "pki"
  type        = "pki"
  description = "PKI secret engine mount"
}

data "vault_pki_secret_backend_config_cmpv2" "cmpv2_config" {
  backend     = vault_mount.pki.path
}
```

## Argument Reference

The following arguments are supported:

* `namespace` - (Optional) The namespace of the target resource.
  The value should not contain leading or trailing forward slashes.
  The `namespace` is always relative to the provider's configured [namespace](/docs/providers/vault/index.html#namespace).
  *Available only for Vault Enterprise*.

* `backend` - (Required) The path to the PKI secret backend to
  read the CMPv2 configuration from, with no leading or trailing `/`s.

# Attributes Reference

* `authenticators` - Lists the mount accessors CMPv2 should delegate authentication requests towards (see [below for nested schema](#nestedatt--authenticators)).

* `default_path_policy` - Specifies the behavior for requests using the non-role-qualified CMPv2 requests. Can be sign-verbatim or a role given by role:<role_name>.

* `enable_sentinel_parsing` - If set, parse out fields from the provided CSR making them available for Sentinel policies.

* `enabled` - Specifies whether CMPv2 is enabled.

* `audit_fields` - Fields parsed from the CSR that appear in the audit and can be used by sentinel policies.

* `disabled_validations` - A comma-separated list of validations not to perform on CMPv2 messages.

* `last_updated` - A read-only timestamp representing the last time the configuration was updated.

<a id="nestedatt--authenticators"></a>
### Nested Schema for `authenticators`

* `cert` - "The accessor and cert_role properties for cert auth backends".