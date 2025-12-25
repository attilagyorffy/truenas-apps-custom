# TrueNAS Custom Apps

A collection of Docker Compose configurations for applications not currently available in the [official TrueNAS app catalog](https://github.com/truenas/apps).

## Purpose

This repository provides ready-to-use Docker Compose configurations for applications that are either:

1. **Not yet in the official catalog** - Applications awaiting integration or review
2. **Not planned for official support** - Applications the TrueNAS team has no intent to maintain officially. For example, see [this discussion](https://github.com/truenas/apps/pull/3783) regarding standalone database servers like MySQL.

## What's Included

Each app directory contains a (mostly) complete Docker Compose configuration with detailed documentation:

- **Tested Configurations**: All included setups have been tested and verified working in production
- **Comprehensive Documentation**: Each app includes a README with deployment instructions, configuration options, and troubleshooting guides
- **TrueNAS Integration**: Deploy directly through the TrueNAS Web UI using YAML installation

## Deployment

These apps are deployed through the TrueNAS Web UI, not via command-line Docker Compose:

1. Navigate to **Apps** → **Discover Apps**
2. Click the three dots next to **Custom App**
3. Select **Install via YAML**
4. Copy the contents of the app's `docker-compose.yml`
5. Review the app's README for required configuration changes
6. Deploy and monitor through the TrueNAS interface

## Apps

- **[Ghost](./ghost/)** - Blogging platform with MySQL backend
- **[MailCatcher](./mailcatcher/)** - SMTP testing and email capture service
- **[Fizzy](./fizzy/)** - Lightweight issue tracking system

## Configuration

Before installing via the TrueNAS Web UI:

1. Choose the app you want to deploy
2. Read the app's README for configuration details and requirements
3. Review the `docker-compose.yml` and identify what needs to be customized for your environment
4. Prepare storage paths and note any required credentials
5. Use the TrueNAS Web UI to paste the YAML configuration
6. Apply your customizations through the UI before deploying

## Contributing

Contributions are welcome! If you have a working Docker Compose configuration for an app not in the official catalog:

1. Create a new directory with your app name
2. Include a `docker-compose.yml` with clear configuration
3. Write a comprehensive README following the existing format
4. Ensure all credentials and sensitive data are clearly marked for user customization
5. Submit a pull request

## Notes

- These configurations have been tested on TrueNAS systems but may require adjustments for your specific environment
- Always review security settings, credentials, and network configurations before deploying to production
- Ensure persistent storage paths exist on your TrueNAS system before deployment
- For issues or improvements, please open an issue or submit a pull request
