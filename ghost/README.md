# Ghost for TrueNAS

Docker Compose configuration for deploying Ghost 6.9.3 with MySQL 8.4.7 on TrueNAS.

> [!NOTE]
> This configuration is based on the [official Ghost Docker setup](https://docs.ghost.org/install/docker) with TrueNAS-specific modifications for persistent storage and local mail testing.

## Services

This deployment includes two containerized services:

| Service | Image | Container Name | Purpose |
|---------|-------|-----------------|---------|
| Ghost | ghost:6.9.3 | ghost | Blogging platform and content management system |
| MySQL | mysql:8.4.7 | ghost-mysql | Database backend for Ghost data persistence |

## Networking

- **Network Name**: `ghost-network`
- **Driver**: bridge
- **Scope**: Both services communicate internally via this isolated network

## Ports

| Service | Port | Protocol | Access |
|---------|------|----------|--------|
| Ghost | 2368 | HTTP | Public/Reverse proxy port |
| MySQL | (internal) | TCP | Not exposed; internal only |

## Volumes

Both services require persistent storage on the TrueNAS host:

| Service | Container Path | Host Path | Purpose |
|---------|-----------------|-----------|---------|
| Ghost | /var/lib/ghost/content | /mnt/tank/apps/ghost/content | Blog posts, pages, assets, and uploads |
| MySQL | /var/lib/mysql | /mnt/tank/apps/ghost/mysql | Database files and data |

**Prerequisites**: Ensure these directories exist on your TrueNAS storage with proper permissions before deployment.

## Configuration

### Ghost Environment Variables

| Variable | Value | Description |
|----------|-------|-------------|
| NODE_ENV | production | Run Ghost in production mode |
| url | https://blog.example.com | Public URL for the Ghost instance |
| database__client | mysql | Database client type |
| database__connection__host | mysql | MySQL container hostname on ghost-network |
| database__connection__database | ghost | Database name to create/use |
| database__connection__user | ghost | MySQL user for Ghost |
| database__connection__password | changeme-ghost-password | MySQL password for Ghost user (⚠️ change this) |
| mail__transport | SMTP | Email delivery method |
| mail__options__host | 10.0.0.3 | MailCatcher host IP for email testing |
| mail__options__port | 1025 | MailCatcher SMTP port |
| mail__options__secure | False | No TLS for internal MailCatcher connection |
| activitypub__enabled | False | Disable ActivityPub federation |

> [!TIP]
> If you only need a basic blog without mail-dependent features like member notifications and subscriptions, you can simplify the mail configuration and deploy [MailCatcher](../mailcatcher/README.md).


## MySQL Environment Variables

| Variable | Value | Description |
|----------|-------|-------------|
| MYSQL_ROOT_PASSWORD | changeme-root-password | MySQL root password (⚠️ change this) |
| MYSQL_DATABASE | ghost | Database to create automatically |
| MYSQL_USER | ghost | User to create for Ghost |
| MYSQL_PASSWORD | changeme-ghost-password | Password for Ghost user (⚠️ change this) |

## Deployment Steps

1. **Create directories** on TrueNAS:
   ```bash
   mkdir -p /mnt/tank/apps/ghost/content
   mkdir -p /mnt/tank/apps/ghost/mysql
   ```

2. **Update credentials** in the docker-compose.yml:
   - Change `MYSQL_ROOT_PASSWORD`
   - Change `MYSQL_PASSWORD`
   - Change `database__connection__password`

3. **Update email configuration**:

   - Modify `mail__options__host` to your SMTP server
   - Update `mail__options__port` as needed
   - Set `mail__options__secure` to True if using TLS

4. **Update the Ghost URL**:
   - Change `url` to your actual domain (e.g., https://yourdomain.com)

5. **Update the mail options**:

Set up proper email configuration (replace MailCatcher with real SMTP in production).

## Troubleshooting

### Ghost fails to start

- Check MySQL is healthy: `docker logs ghost-mysql`
- Verify volumes exist and have proper permissions
- Check database credentials match between services

### MySQL fails to start

- Ensure `/mnt/tank/apps/ghost/mysql` exists and is writable
- Review MySQL logs: `docker logs ghost-mysql`

### Can't connect to Ghost

- Verify port 2368 is exposed and accessible
- Check reverse proxy/firewall settings if using one
- Verify the `url` variable matches your access URL
