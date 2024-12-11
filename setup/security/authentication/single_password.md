---
description: SUSE Observability Self-hosted
---

# Single password

Out of the box, SUSE Observability is configured with a single password configuration, [configured during installation](/k8s-suse-rancher-prime.md#installation). This authenticates users with a single, randomly generated password created by the `suse-observability-values` chart.
The password value can be found as comment in the generated `suse-observability-values/templates/baseConfig_values.yaml`.

This setup provisions an admin user with the generated password

This method for providing a password is suited to get started out-of-the-box, but for production one of the other [authentication options](/setup/security/authentication/authentication_options.md) is recommended.

## Setting the single password through the values chart

To set an alternative password [during installation](/k8s-suse-rancher-prime.md#installation), add the following line to the values generation command during installation:

```bash
helm template \
  ...
  --set adminPassword='<password>' \
  ...
  suse-observability-values \
  suse-observability/suse-observability-values --output-dir $VALUES_DIR
```

## Setting the single password in the helm chart

It is also possible to set the single password directly in the helm install invocation. This is done by creating a bcrypted string of the required password, using `htpasswd -bnBC 10 "" <password> | tr -d ':\n'` or using an online tool.
The fill it into the helm invocation or separate values.yaml.

```bash
helm install \
  ...
  --set stackstate.authentication.adminPassword='<bcrypted_password>' \
  ...
  suse-observability \
  suse-observability/suse-observability --output-dir $VALUES_DIR
```

### Using an external secret

When the single password should come from an external secret, follow [these steps](/setup/security/external-secrets.md#getting-authentication-data-from-an-external-secret) but fill in the following data:

```yaml
kind: Secret
metadata:
   name: "<custom-secret-name>"
type: Opaque
data:
  default_password: <base64 of bcrypted password>
```

Creating a bcrypted version of a password can be done using `htpasswd -bnBC 10 "" <password> | tr -d ':\n'`.

## See also

* [Authentication options](authentication_options.md)
* [Permissions for predefined SUSE Observability roles](../rbac/rbac_permissions.md#predefined-roles)
* [Create RBAC roles](../rbac/rbac_roles.md)
* [External Secrets](/setup/security/external-secrets.md#getting-authentication-data-from-an-external-secret)

