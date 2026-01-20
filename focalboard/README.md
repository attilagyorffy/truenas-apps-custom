# Focalboard for TrueNAS Scale

A Docker Compose configuration for running Focalboard on TrueNAS Scale Fangtooth.

## What is Focalboard?

Focalboard is an open source, self-hosted alternative to Trello, Notion, and Asana. It helps define, organize, track, and manage work across teams using a familiar kanban board interface.

## Features

- Kanban boards for project management
- Table and calendar views
- Cards with properties, checklists, and comments
- Filter, sort, and group cards
- User permissions and sharing
- Self-hosted and privacy-focused

## Prerequisites

- TrueNAS Scale Fangtooth
- Docker and Docker Compose support enabled
- Available port: 3002 (Web UI)

## Installation

### Prepare Data Directory

Before deploying, create the data directory with proper permissions:

```bash
# Create the directory structure
mkdir -p /mnt/tank/apps/focalboard/data

# Set ownership to nobody:nogroup (UID:GID 65534:65534)
chown -R 65534:65534 /mnt/tank/apps/focalboard/data

# Set appropriate permissions
chmod -R 755 /mnt/tank/apps/focalboard/data
```

**Important**: The container runs as the `nobody` user (UID 65534). The data directory must have correct ownership before starting the container.

### Option 1: Using TrueNAS Scale Custom App

1. Navigate to **Apps** in TrueNAS Scale
2. Click on **Discover Apps**
3. Select **Custom App**
4. Configure the following:
   - **Application Name**: focalboard
   - **Image Repository**: mattermost/focalboard
   - **Image Tag**: latest
   - **Port Forwarding**:
     - Container Port: 8000 → Node Port: 3002 (Web UI)
   - **Storage**: Mount `/opt/focalboard/data` to persistent storage
   - **Restart Policy**: Unless Stopped

### Option 2: Using Docker Compose

1. Copy the `docker-compose.yml` file to your TrueNAS system
2. Create the data directory as shown above
3. Run:
   ```bash
   docker-compose up -d
   ```

## Usage

### Accessing the Web Interface

Once running, access the Focalboard web interface at:
```
http://your-truenas-ip:3002
```

On first access, you'll be able to create an account and start using Focalboard.

### Creating Your First Board

1. Log in to the web interface
2. Click "Create a board" or use a template
3. Add lists/columns to organize your workflow
4. Create cards for tasks and projects
5. Drag and drop cards between columns

## Ports

- **3002**: Web interface port (maps to container port 8000)

**Note**: If this port conflicts with other services, modify it in the `docker-compose.yml` file:
```yaml
ports:
  - "3003:8000"  # Use port 3003 instead of 3002
```

## Data Persistence

All data is stored in `/mnt/tank/apps/focalboard/data`. This includes the SQLite database and uploaded files. Ensure this directory exists and has appropriate permissions:

```bash
mkdir -p /mnt/tank/apps/focalboard/data
chmod -R 755 /mnt/tank/apps/focalboard/data
```

## Troubleshooting

### Can't Access Web Interface

1. Verify the container is running:
   ```bash
   docker ps | grep focalboard
   ```

2. Check the logs for errors:
   ```bash
   docker logs focalboard
   ```

3. Ensure port 3002 is not blocked by firewall rules

### Permission Issues

If you encounter permission errors with the data volume (e.g., "unable to open database file"):
```bash
chown -R 65534:65534 /mnt/tank/apps/focalboard/data
chmod -R 755 /mnt/tank/apps/focalboard/data
```

The container runs as the `nobody` user with UID:GID 65534:65534.

### Database Errors

Focalboard uses SQLite by default. If you encounter database corruption:
1. Stop the container
2. Back up the data directory
3. Check the `focalboard.db` file in the data directory

## Updating

To update to the latest version:

```bash
docker-compose pull
docker-compose up -d
```

## Additional Resources

- [Focalboard Official Documentation](https://www.focalboard.com/docs/)
- [Focalboard GitHub Repository](https://github.com/mattermost-community/focalboard)
- [Docker Hub - Focalboard](https://hub.docker.com/r/mattermost/focalboard)
