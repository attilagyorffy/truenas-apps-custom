# Uptime Kuma for TrueNAS Scale

A Docker Compose configuration for running Uptime Kuma on TrueNAS Scale Fangtooth.

## What is Uptime Kuma?

Uptime Kuma is a self-hosted monitoring tool that allows you to monitor your websites, APIs, and services. It provides a clean web interface with status pages, notifications, and uptime statistics.

## Features

- Monitor HTTP(s), TCP, Ping, DNS, and more
- Beautiful status page
- Multiple notification channels (Discord, Slack, Email, etc.)
- Multi-language support
- 2FA authentication
- Simple, reactive UI

## Prerequisites

- TrueNAS Scale Fangtooth
- Docker and Docker Compose support enabled
- Available port: 3001 (Web UI)

## Installation

### Prepare Data Directory

Before deploying, create the data directory with proper permissions:

```bash
# Create the directory
mkdir -p /mnt/tank/apps/uptime-kuma/data

# Set ownership to UID:GID 1000:1000 (node user inside container)
chown -R 1000:1000 /mnt/tank/apps/uptime-kuma/data

# Set appropriate permissions
chmod -R 755 /mnt/tank/apps/uptime-kuma/data
```

**Note**: The Uptime Kuma container runs as the `node` user with UID:GID 1000:1000 by default.

### Option 1: Using TrueNAS Scale Custom App

1. Navigate to **Apps** in TrueNAS Scale
2. Click on **Discover Apps**
3. Select **Custom App**
4. Configure the following:
   - **Application Name**: uptime-kuma
   - **Image Repository**: louislam/uptime-kuma
   - **Image Tag**: 2
   - **Port Forwarding**:
     - Container Port: 3001 → Node Port: 3001 (Web UI)
   - **Storage**: Mount `/app/data` to persistent storage
   - **Restart Policy**: Unless Stopped

## Usage

### Accessing the Web Interface

Once running, access the Uptime Kuma web interface at:
```
http://your-truenas-ip:3001
```

On first access, you'll be prompted to create an admin account.

### Adding Monitors

1. Log in to the web interface
2. Click "Add New Monitor"
3. Configure monitor type (HTTP(s), TCP, Ping, etc.)
4. Set check interval and notification preferences
5. Save the monitor

## Ports

- **3001**: Web interface port

**Note**: If this port conflicts with other services, modify it in the `docker-compose.yml` file:
```yaml
ports:
  - "3002:3001"  # Use port 3002 instead of 3001
```

## Data Persistence

All data is stored in `/mnt/tank/apps/uptime-kuma/data`. Ensure this directory exists and has appropriate permissions:

```bash
mkdir -p /mnt/tank/apps/uptime-kuma/data
chown -R 1000:1000 /mnt/tank/apps/uptime-kuma/data
```

## Troubleshooting

### Can't Access Web Interface

1. Verify the container is running:
   ```bash
   docker ps | grep uptime-kuma
   ```

2. Check the logs for errors:
   ```bash
   docker logs uptime-kuma
   ```

3. Ensure port 3001 is not blocked by firewall rules

### Permission Issues

If you encounter permission errors with the data volume:
```bash
chown -R 1000:1000 /mnt/tank/apps/uptime-kuma/data
```

## Updating

To update to the latest version:

```bash
docker-compose pull
docker-compose up -d
```

## Additional Resources

- [Uptime Kuma Official Documentation](https://uptimekuma.org/)
- [Uptime Kuma GitHub Repository](https://github.com/louislam/uptime-kuma)
- [Docker Hub - Uptime Kuma](https://hub.docker.com/r/louislam/uptime-kuma)
