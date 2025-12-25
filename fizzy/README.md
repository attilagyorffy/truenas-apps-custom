# Fizzy for TrueNAS

Docker Compose configuration for [Fizzy](https://github.com/basecamp/fizzy), an open-source Kanban board from Basecamp, optimized for TrueNAS SCALE.

## Important Notes

- The container runs as UID/GID 1000 internally
- The container needs write access to both `/rails/storage` and `/rails/tmp`
- Both storage directories must be owned by UID 1000

## Prerequisites

- TrueNAS SCALE with Apps/Docker support
- A dataset for persistent storage (e.g., `/mnt/tank/apps/fizzy`)

## Setup instructions

### 1. Prepare the filesystem

SSH into your TrueNAS server and set up the required datasets/directories and permissions:

```bash
# Create required directories
mkdir -p /mnt/tank/apps/fizzy/storage
mkdir -p /mnt/tank/apps/fizzy/tmp

# Set ownership to UID 1000 (required by the Fizzy container)
chown -R 1000:1000 /mnt/tank/apps/fizzy/storage
chown -R 1000:1000 /mnt/tank/apps/fizzy/tmp

# Set permissions
chmod -R 755 /mnt/tank/apps/fizzy/storage
chmod -R 755 /mnt/tank/apps/fizzy/tmp
```

### 2. Generate a secret key

```bash
# Generate a 128-character secret key
openssl rand -hex 64
```

Save this key - you'll need it for the configuration.

### 3. Proceed with the rest of the configuration

## Configuration

All configuration is done through environment variables.

### Required Environment Variables

These variables must be set in your `docker-compose.yml` or TrueNAS app configuration:

- `SECRET_KEY_BASE`: A 128-character secret key (generated in step 2: Generate a secret key)
- `BASE_URL`: The URL where Fizzy will be accessed (e.g., `http://[your-truenas-ip]:3000`)
- `DISABLE_SSL`: Set to `true` for local/non-HTTPS deployments

### Email Configuration (Optional)

To enable email notifications, configure these SMTP variables:

- `MAILER_FROM_ADDRESS`: Sender email address (e.g., `fizzy@example.com`)
- `SMTP_ADDRESS`: Your SMTP server (e.g., `10.0.0.3` or `smtp.gmail.com`)
- `SMTP_PORT`: SMTP port (typically `25`, `587`, or `465`)
- `SMTP_TLS`: Set to `true` for TLS/SSL connections, `false` for unencrypted
- `SMTP_USERNAME`: Your SMTP username (leave empty if not required)
- `SMTP_PASSWORD`: Your SMTP password (leave empty if not required)

### Advanced Configuration (Optional)

### Multi-tenant Mode
- `MULTI_TENANT`: Set to `true` to allow multiple accounts/organizations

### S3 Storage

To use S3-compatible storage instead of local storage:

- `ACTIVE_STORAGE_SERVICE`: Set to `s3`
- `S3_BUCKET`: Your S3 bucket name
- `S3_REGION`: AWS region (e.g., `us-east-1`)
- `S3_ACCESS_KEY_ID`: Your AWS/S3 access key
- `S3_SECRET_ACCESS_KEY`: Your AWS/S3 secret key

## Deployment

1. Go to **Apps** → **Discover Apps** -> Hit the three dots next to **Custom App** and select **Install via YAML**

2. Set the app name and use the contents of `docker-compose.yml` with the updated environment variables set from the Configuration section.

3. Hit save. Once deployed, access Fizzy at: `http://[your-truenas-ip]:3000`

## Troubleshooting

### 1. Missing SECRET_KEY_BASE

**Error:**

```
ArgumentError: Missing `secret_key_base` for 'production' environment
```

**Fix:** Ensure you've provided the `SECRET_KEY_BASE` environment variable in your TrueNAS app configuration. Generate one using:

```bash
openssl rand -hex 64
```

### 2. Cannot Create Database

**Error:**

```
SQLite3::CantOpenException: unable to open database file
```

**Fix:** Ensure `/rails/storage` is writable by UID 1000:

```bash
chown -R 1000:1000 /mnt/tank/apps/fizzy/storage
chmod -R 755 /mnt/tank/apps/fizzy/storage
```

### 3. Cannot Create Sockets Directory

**Error:**

```
Permission denied @ dir_s_mkdir - /rails/tmp/sockets
```

**Fix:** Ensure `/rails/tmp` is writable by UID 1000:

```bash
chown -R 1000:1000 /mnt/tank/apps/fizzy/tmp
chmod -R 755 /mnt/tank/apps/fizzy/tmp
```

### Backup Data

```bash
# Create backup (via SSH)
tar -czf fizzy-backup-$(date +%Y%m%d).tar.gz -C /mnt/tank/apps/fizzy storage
```
