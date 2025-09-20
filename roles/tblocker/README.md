# Ansible Role: tblocker

Deploys and manages xray-torrent-blocker (tblocker) using package manager installation.

## Description

This role automates the deployment of tblocker, a service that monitors Xray logs for torrent activity and blocks offending IP addresses. It installs tblocker from the official repository and configures it for use with Remnawave and other Xray-based panels.

## Requirements

- Ansible >= 2.9
- Ubuntu/Debian system (for APT package installation)
- Xray with proper logging configuration
- Firewall support (iptables or nftables)

## Role Variables

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `tblocker_log_file` | Path to Xray access log file | `"/var/log/remnanode/access.log"` |

### Optional Variables

| Variable | Description | Default | Notes |
|----------|-------------|---------|-------|
| `tblocker_block_duration` | Block duration in minutes | `10` | How long IPs stay blocked |
| `tblocker_torrent_tag` | Tag to identify torrent traffic | `"TORRENT"` | Must match Xray routing config |
| `tblocker_block_mode` | Firewall to use | `"nftables"` | `"iptables"` or `"nftables"` |
| `tblocker_storage_dir` | Directory for block data | `"/opt/tblocker"` | Persistent storage location |
| `tblocker_username_regex` | Regex for username extraction | `"email: (\\S+)"` | For Marzban use `"^\\d+\\.(.+)$"` |
| `tblocker_send_webhook` | Enable webhook notifications | `true` | Send notifications to external systems |
| `tblocker_webhook_url` | Webhook endpoint URL | `"https://your-api-endpoint.com/..."` | Your webhook receiver |
| `tblocker_webhook_template` | JSON template for webhook | `'{"username":"%s",...}'` | Customizable payload format |
| `tblocker_webhook_headers` | Additional HTTP headers | `{}` | Authentication headers, etc. |

## Example Playbook

### Basic Usage

```yaml
- hosts: xray_servers
  become: true
  vars:
    tblocker_log_file: "/var/log/remnanode/access.log"
    tblocker_block_duration: 15
    tblocker_webhook_url: "https://your-api.com/webhook"
  roles:
    - lagomvpn.xray.tblocker
```

### Advanced Configuration

```yaml
- hosts: xray_servers
  become: true
  vars:
    tblocker_log_file: "/var/log/remnanode/access.log"
    tblocker_block_duration: 30
    tblocker_block_mode: "iptables"
    tblocker_send_webhook: true
    tblocker_webhook_url: "https://api.example.com/ban"
    tblocker_webhook_headers:
      Authorization: "Bearer {{ webhook_token }}"
      Content-Type: "application/json"
    # For Marzban panels:
    tblocker_username_regex: "^\\d+\\.(.+)$"
  roles:
    - lagomvpn.xray.tblocker
```

## Xray Configuration

To use tblocker, your Xray configuration must include:

### Routing Rules

Add bittorrent detection to your routing section:

```json
{
  "routing": {
    "rules": [
      {
        "protocol": ["bittorrent"],
        "outboundTag": "TORRENT",
        "type": "field"
      }
    ]
  }
}
```

### Outbound Configuration

Add a blackhole outbound for torrent traffic:

```json
{
  "outbounds": [
    {
      "protocol": "blackhole",
      "tag": "TORRENT"
    }
  ]
}
```

### Logging Configuration

Enable access logging:

```json
{
  "log": {
    "access": "/var/log/remnanode/access.log",
    "error": "/var/log/remnanode/error.log",
    "loglevel": "warning"
  }
}
```

## Features

- **Automatic Installation**: Installs from official repository
- **Configurable Blocking**: Support for iptables and nftables
- **Webhook Integration**: Send notifications to external systems
- **Persistent Storage**: Block state survives restarts
- **Automatic Cleanup**: Expired blocks are automatically removed
- **Connection Termination**: Uses conntrack to drop existing connections

## Supported Panels

- **Remnawave**: Default configuration works out of the box
- **Marzban**: Set `tblocker_username_regex: "^\\d+\\.(.+)$"`
- **Other Xray panels**: Ensure proper logging and routing configuration

## Service Management

The role automatically:
- Installs and configures tblocker
- Creates systemd service
- Starts and enables the service
- Handles configuration updates with service restart

View logs:
```bash
journalctl -u tblocker -f --no-pager
```

## Author Information

This role is part of the lagomvpn.xray collection created by the LagomVPN Team.

For tblocker source code and issues: https://github.com/kutovoys/xray-torrent-blocker 