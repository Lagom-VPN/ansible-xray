# Ansible Collection: lagomvpn.remnawave

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-lagomvpn.remnawave-blue.svg)](https://galaxy.ansible.com/lagomvpn/remnawave)
[![License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE)

An Ansible collection containing roles for managing Remnawave infrastructure.

## Description

This collection provides roles for managing Remnawave infrastructure components. It includes tools for deploying, configuring, and managing various Remnawave services using Docker containers.

## Included Roles

### `lagomvpn.remnawave.remnanode`

Deploys and manages Remnawave nodes using Docker Compose. Handles SSL certificate management, node registration, and service deployment with the Remnawave management panel.

## Installation

```bash
ansible-galaxy collection install lagomvpn.remnawave
```

## Dependencies

- `community.docker` >= 3.4.0

## License

MIT

## Author Information

This collection was created by the LagomVPN Team.

For support and contributions, visit: [GitHub Repository](https://github.com/Lagom-VPN/ansible-remnawave)
