# MailCatcher for TrueNAS

Docker Compose configuration for deploying MailCatcher as an SMTP email capture service on TrueNAS.

> [!NOTE]
> This configuration is based on the [official MailCatcher Docker image](https://hub.docker.com/r/sj26/mailcatcher).

## Service

| Component | Image | Version | Container Name |
|-----------|-------|---------|-----------------|
| MailCatcher | sj26/mailcatcher | latest | mailcatcher |

## Networking

- **Network Name**: `ix-n8n_default`
- **Type**: External (shared with other applications)
- **Purpose**: Allows other services to communicate with MailCatcher

## Ports

| Port | Purpose | Protocol |
|------|---------|----------|
| 1025 | SMTP server | TCP |
| 1080 | Web interface | HTTP |

## Configuration

### Environment

No environment variables required. MailCatcher works out-of-the-box with sensible defaults.


## SMTP Configuration for Applications

Configure sending applications with:

| Setting | Value |
|---------|-------|
| Host | mailcatcher (or container IP) |
| Port | 1025 |
| Authentication | None |
| TLS/SSL | Disabled |

## Accessing the Web Interface

Once deployed, access the email capture interface at:

```
http://<truenas-ip>:1080
```

View all captured emails, inspect headers, and download message contents.

## Use Cases

- **Development Testing**: Capture emails from Ghost and other applications without sending to real addresses
- **Integration Testing**: Verify email content and headers before production deployment
- **Staging Environments**: Non-destructive email capture for testing workflows

## Network Integration

MailCatcher connects to the `ix-n8n_default` network, making it accessible to other truenas apps (when configured to use mailcatcher:1025).

To use MailCatcher with services on different networks, either:
1. Add MailCatcher to their network
2. Use the host's IP address (e.g., `10.0.0.3:1025`) for cross-network communication

## Important Notes

⚠️ **Development Only**: MailCatcher is designed for development and testing. Do not use in production for real email delivery.

⚠️ **No Persistence**: Captured emails are stored in memory and lost when the container restarts. This is intentional for a testing tool.

⚠️ **Authentication**: No authentication or authorization controls. Only deploy on trusted networks.
