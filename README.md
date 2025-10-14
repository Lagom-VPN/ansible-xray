# Ansible Collection: lagomvpn.xray

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-lagomvpn.xray-blue.svg)](https://galaxy.ansible.com/lagomvpn/xray)
![License](https://img.shields.io/badge/license-MIT-green.svg)

An Ansible collection containing roles for managing Xray infrastructure.

## Overview

This collection provides roles for managing Xray infrastructure components. It includes tools for deploying, configuring, and managing various Xray services using Docker containers.

## Roles

### `lagomvpn.xray.remnanode`

Deploys and manages Remnawave nodes using Docker Compose. Handles SSL certificate management, node registration, and service deployment with the Remnawave management panel.

### `lagomvpn.xray.tblocker`

Deploys and manages xray-torrent-blocker service for monitoring Xray logs and blocking torrent traffic. Supports webhook notifications and integrates with Remnawave and other Xray-based panels.

## Installation

```bash
ansible-galaxy collection install lagomvpn.xray
```

## Requirements

- Ansible >= 2.9
- Docker and Docker Compose on target hosts
- Python docker library

## Development

### Publishing to Ansible Galaxy

This collection is automatically published to Ansible Galaxy when a new release is created on GitHub.

#### Setup

1. Generate an API key from [Ansible Galaxy](https://galaxy.ansible.com/me/preferences)
2. Add the API key to GitHub repository secrets as `GALAXY_API_KEY`:
   - Go to your repository → Settings → Secrets and variables → Actions
   - Click "New repository secret"
   - Name: `GALAXY_API_KEY`
   - Value: Your Ansible Galaxy API key

#### Creating a Release

1. Update the version in `galaxy.yml` (optional - workflow can do this automatically based on tag)
2. Create and push a new tag:
   ```bash
   git tag -a v1.0.1 -m "Release version 1.0.1"
   git push origin v1.0.1
   ```
3. Create a release on GitHub from the tag
4. The workflow will automatically:
   - Build the collection
   - Publish it to Ansible Galaxy
   - Upload the build artifact to the release

## License

MIT

## Support

For support and contributions, visit: [GitHub Repository](https://github.com/Lagom-VPN/ansible-xray)
