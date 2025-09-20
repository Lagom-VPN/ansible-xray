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

## License

MIT

## Support

For support and contributions, visit: [GitHub Repository](https://github.com/Lagom-VPN/ansible-xray)
