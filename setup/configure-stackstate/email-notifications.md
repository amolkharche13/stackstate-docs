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
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; When the username and password cannot be provided in the values but should come from an external secret, follow [these steps](/setup/security/external-secrets.md#getting-username-and-password-for-email-sending-from-an-external-secret)



To apply the changes, use the command: helm upgrade by passing --values values.yaml,

This will use port `587` on the SMTP server and uses the `STARTTLS` command to establish a secure connection.


Once the changes are applied, navigate to the SUSE Observability URL, then go to **Notifications** → **Add New Notification**. 
Fill in the required details, select **Email** as the channel, and click **Add Channels** when you're done.
Finally, click the ▶️ TEST button to verify if your configuration is correct.

![image](https://github.com/user-attachments/assets/7affa8c3-596c-477d-9591-6140fee1c431)
![image](https://github.com/user-attachments/assets/6827310e-f991-4ade-9f24-78c1f5dac282)
![image](https://github.com/user-attachments/assets/0b5c730f-51bf-403f-926c-a5d1ebe283bd)

If your configuration is correct, you will receive a test email.

![image](https://github.com/user-attachments/assets/6034f6b4-9254-4005-844f-f6fe2c52436e)
![image](https://github.com/user-attachments/assets/bfef3caf-19c2-47eb-bad9-38913afeb984)


### Troubleshooting
1. If you encounter an error message when clicking the ▶️ TEST button, check the logs on your SMTP server, typically located under `/var/log/mail.log`. You should find some clues to help resolve the issue.
   
![image](https://github.com/user-attachments/assets/85af37e9-86ec-4668-9cbf-d8430d07071c)

2. If you encounter the _disconnect from unknown_ error message in the SMTP server logs, you will need to add the cluster node IP to the `main.cf` configuration file.
   
```
    postfix/smtpd[7635]: disconnect from unknown[165.232.178.159] ehlo=2 starttls=1 mail=1 rcpt=0/1 rset=1 quit=1 commands=6/7 
    postfix/smtpd[7635]: connect from unknown[165.232.178.159]
```
```
    cat /etc/postfix/main.cf|grep -i mynetworks
    mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 165.232.178.159/32 165.232.190.163/32
```
3. If you encounter the _Relay access denied_ error message in the SMTP server logs, The error message Relay access denied typically occurs when your Postfix SMTP server rejects an email due to misconfigured relay settings. 
```
   postfix/smtpd[6310]: NOQUEUE: reject: RCPT from unknown[165.232.178.159]: 454 4.7.1 Relay access denied; from=<example.com> to=<user1> proto=ESMTP helo=<suse-observability-server-8f6866c5d-46bth>
```
     Try adding the domain to relay_domains in the `main.cf`: 
```
   relay_domains = example.com
```
4. If you encounter the error below, it indicates a connection issue between the client (or sender) and the SMTP server. To troubleshoot, test the connection between the servers by running the following command: `telnet smtp.example.com 25`.
```
        "message": "Operation timeout error.",
        "errorCode": 408,
        "_type": "ServerTimeoutError"
```
