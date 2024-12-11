---
description: SUSE Observability Self-hosted
---

# Configuring SUSE Observability for E-mail notifications

{% hint style="warning" %}
SaaS users of SUSE Observability can use E-mail notifications without extra configuration. This guide is only applicable for self-hosted SUSE Observability installations, that are planning to use the E-mail notification channel.
{% endhint %}

Before you can use the E-mail notification channel in SUSE Observability, you first need to follow the following steps:

## Configure SUSE Observability with the SMTP configuration

SUSE Observability needs to be configured with credentials to connect to the SMTP server. You can do this by adding the following to the `values.yaml` file of your SUSE Observability installation:

```yaml
stackstate:
  email:
    enabled: true
    sender: "<stackstate@example.com>"
    server:
      host: "<smtp.example.com>"
      auth:
        username: "<user name>"
        password: "<user password>"
```

This will use port `587` on the SMTP server and uses the `STARTTLS` command to establish a secure connection. These are all the other options that can be customized:

```yaml
stackstate:
  email:
    additionalProperties: 
      # Add needed Java email properties for your mail server (use string values), defaults are: 
      "mail.smtp.auth": "true"
      "mail.smtp.starttls.enable": "true"
    server:
      protocol: smtp
      port: 587
```

### Using an external secret

When the username and password cannot be provided in the values but should come from an external secret, follow [these steps](/setup/security/external-secrets.md#getting-username-and-password-for-email-sending-from-an-external-secret)