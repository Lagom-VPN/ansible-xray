# Ansible Role: tblocker

Deploys and manages xray-torrent-blocker (tblocker) using package manager installation.

## Description

This role automates the deployment of tblocker, a service that monitors Xray logs for torrent activity and blocks offending IP addresses. It installs tblocker from the official repository and configures it for use with Remnawave and other Xray-based panels.

## Requirements

- Ansible >= 2.9
- Ubuntu/Debian system (for APT package installation)
- Xray with proper logging configuration

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
| `tblocker_block_mode` | Firewall to use | `"nft"` | `"iptables"` or `"nft"` |
| `tblocker_config_dir` | Main directory for tblocker | `"/opt/tblocker"` | Contains binary, config, and storage |
| `tblocker_storage_dir` | Directory for block data | `"{{ tblocker_config_dir }}"` | Defaults to same as config dir |
| `tblocker_config_file` | Full path to config file | `"{{ tblocker_config_dir }}/config.yaml"` | Auto-generated from config dir |
| `tblocker_binary_path` | Path to tblocker binary | `"{{ tblocker_config_dir }}/tblocker"` | Auto-generated from config dir |
| `tblocker_username_regex` | Regex for username extraction | `"email: (\\S+)"` | For Marzban use `"^\\d+\\.(.+)$"` |
| `tblocker_send_webhook` | Enable webhook notifications | `true` | Send notifications to external systems |
| `tblocker_webhook_url` | Webhook endpoint URL | `"https://your-api-endpoint.com/..."` | Your webhook receiver |
| `tblocker_webhook_template` | JSON template for webhook | `'{"username":"%s",...}'` | Customizable payload format |
| `tblocker_webhook_headers` | Additional HTTP headers | `{}` | Authentication headers, etc. |

**Note**: For most use cases, you only need to set `tblocker_config_dir`. All other paths will be automatically derived from it. Advanced users can override individual paths if needed.

## Configuration Examples

### Basic Configuration

```yaml
tblocker_config_dir: "/opt/tblocker"
tblocker_log_file: "/var/log/remnanode/access.log"
tblocker_block_duration: 30
```

### Advanced Configuration with Webhooks

```yaml
tblocker_config_dir: "/opt/tblocker"
tblocker_log_file: "/var/log/remnanode/access.log"
tblocker_block_duration: 30
tblocker_block_mode: "nft"
tblocker_send_webhook: true
tblocker_webhook_url: "https://api.example.com/ban"
tblocker_webhook_headers:
  Authorization: "Bearer {{ webhook_token }}"
  Content-Type: "application/json"
```

## Dependencies

- `community.general` >= 5.0.0

## Supported Platforms

- Ubuntu 20.04+ (Focal, Jammy, Noble)
- Debian 11+ (Bullseye, Bookworm)

## Example Usage

```yaml
- hosts: xray_servers
  become: true
  vars:
    tblocker_log_file: "/var/log/remnanode/access.log"
    tblocker_block_duration: 15
  roles:
    - lagomvpn.xray.tblocker
```

View logs:
```bash
journalctl -u tblocker -f --no-pager
```

## Author Information

This role is part of the lagomvpn.xray collection created by the LagomVPN Team.

For tblocker source code and issues: https://github.com/kutovoys/xray-torrent-blocker 