# Ansible Role: remnanode

Deploys and manages Remnawave nodes using Docker Compose.

## Description

This role automates the deployment of Remnawave nodes, services that integrate with the Remnawave management panel. It handles SSL certificate management, node registration, and service deployment using Docker containers.

## Requirements

- Ansible >= 2.9
- Docker and Docker Compose v2 installed on target hosts
- Access to a Remnawave management panel
- Valid API token for Remnawave integration

## Role Variables

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `remnanode_remnawave_panel_domain` | Domain of your Remnawave panel | `"panel.example.com"` |
| `remnanode_remnawave_api_token` | API token for Remnawave authentication | `"your-api-token-here"` |

### Basic Configuration

| Variable | Description | Default | Possible Values |
|----------|-------------|---------|-----------------|
| `remnanode_project_dir` | Directory for docker-compose files | `/opt/remnanode` | Any valid path |
| `remnanode_ssl_cert_mode` | SSL certificate management mode | `smart` | `smart`, `manual` |

### Node Configuration

| Variable | Description | Default | Notes |
|----------|-------------|---------|-------|
| `remnanode_node.register` | Auto-register node with panel | `true` | `true`, `false` |
| `remnanode_node.country_code` | ISO country code for node | `US` | 2-letter ISO code |
| `remnanode_node.profile.name` | Profile name to use | `default_profile` | Must exist in panel |
| `remnanode_node.profile.inbounds` | List of inbound configurations | `[{tag: "default-inbound"}]` | Array of inbound objects |

### API Configuration

| Variable | Description | Default | Notes |
|----------|-------------|---------|-------|
| `remnanode_remnawave_api_headers` | HTTP headers for API requests | `Authorization: Bearer token` | Can add custom headers |

### Environment Variables

| Variable | Description | Default | Notes |
|----------|-------------|---------|-------|
| `remnanode_env.APP_PORT` | Application port inside container | `2222` | Any valid port number |

### Docker Configuration

| Variable | Description | Default | Notes |
|----------|-------------|---------|-------|
| `remnanode_compose.image` | Docker image name | `remnawave/node` | Container image |
| `remnanode_compose.tag` | Docker image tag | `latest` | Image version |
| `remnanode_compose.volumes` | Volume mount configurations | See volume table below | Array of volume objects |

### Volume Configuration

Each volume in `remnanode_compose.volumes` supports these parameters:

| Parameter | Description | Required | Default | Values |
|-----------|-------------|----------|---------|--------|
| `type` | Volume type | Yes | - | `dir`, `file` |
| `host` | Host path | Yes | - | Any valid path |
| `container` | Container path | Yes | - | Any valid path |
| `mode` | Mount mode | Yes | - | `ro`, `rw` |
| `owner` | File/directory owner | No | `root` | Any valid user |
| `group` | File/directory group | No | `root` | Any valid group |
| `dir_mode` | Directory permissions (dir type only) | No | `0755` | Octal permissions |
| `create` | Auto-create directory (dir type only) | No | `false` | `true`, `false` |

### Data Files Configuration

Each item in `remnanode_dat_files` supports these parameters:

| Parameter | Description | Required | Values | Notes |
|-----------|-------------|----------|--------|-------|
| `name` | File identifier | Yes | Any string | For logging/identification |
| `src_type` | Source type | Yes | `url`, `local` | How to obtain the file |
| `src` | Source location | Yes | URL or local path | Download URL or controller path |
| `dest` | Destination path | Yes | Any valid path | Where to place file on target |
| `mode` | File permissions | No | `0644` | Octal permissions |
| `owner` | File owner | No | `root` | Any valid user |
| `group` | File group | No | `root` | Any valid group |

## Configuration Examples

### Basic Configuration

```yaml
remnanode_remnawave_panel_domain: "panel.example.com"
remnanode_remnawave_api_token: "{{ vault_remnanode_api_token }}"
remnanode_node:
  register: true
  country_code: "DE"
  profile:
    name: "production_profile"
    inbounds:
      - tag: "reality-main"
      - tag: "reality-backup"
```

### Example Volume Configuration

```yaml
remnanode_compose:
  volumes:
    # Standard data directory
    - type: dir
      host: /var/lib/remnanode
      container: /var/lib/remnanode
      mode: rw
      create: true
    # Log directory with specific permissions
    - type: dir
      host: /var/log/remnanode
      container: /var/log/remnanode
      mode: rw
      owner: syslog
      group: adm
      dir_mode: "0755"
      create: true
    # Mount configuration file
    - type: file
      host: /etc/remnanode/app.conf
      container: /var/lib/remnanode/app.conf
      mode: ro
```

### Example Data Files

```yaml
remnanode_dat_files:
  # Download from URL
  - name: geoip
    src_type: url
    src: "https://github.com/v2fly/geoip/releases/latest/download/geoip.dat"
    dest: /var/lib/remnanode/geoip.dat
    mode: "0644"
  # Copy from local file
  - name: custom_rules
    src_type: local
    src: "/path/on/controller/custom.dat"
    dest: /var/lib/remnanode/custom.dat
    mode: "0600"
    owner: remnanode
```

### Example API Headers

You can add custom HTTP headers for API communication:

```yaml
remnanode_remnawave_api_headers:
  Authorization: "Bearer {{ remnanode_remnawave_api_token }}"
  User-Agent: "MyCustomAgent/1.0"
  X-Custom-Header: "custom-value"
  Content-Type: "application/json"  # Added automatically for smart mode
```

### Volume Types

The role supports two types of volume mounting:

- **Directory volumes** (`type: dir`): Mount entire directories. Use `create: true` to automatically create the directory on the host.
- **File volumes** (`type: file`): Mount individual files. The file must exist on the host before mounting.

### Data Files

You can configure files to be downloaded or copied:

- **URL sources** (`src_type: url`): Files downloaded from remote URLs
- **Local sources** (`src_type: local`): Files copied from the Ansible controller

## Dependencies

- `community.docker` >= 3.4.0

## SSL Certificate Management

The role manages SSL certificates for secure communication between the node and Remnawave panel.

### Smart Mode (Recommended)
- **Automatic certificate retrieval**: Fetches SSL certificates directly from Remnawave API
- **Certificate validation**: Verifies certificate integrity before use
- **Intelligent caching**: Reuses existing certificates if they match API response (idempotent)
- **Requirements**: Valid API token with certificate access permissions

### Manual Mode
- **Pre-configured certificates**: Use certificates you provide manually
- **Configuration**: Set `remnanode_ssl_cert_mode: manual`
- **Certificate source**: Provide base64-encoded certificate in `remnanode_env.SSL_CERT`
- **Format**: SSL_CERT value is automatically quoted in the .env file to handle base64 content safely
- **Use case**: When you want full control over certificate management

## Security Considerations

- Store API tokens in Ansible Vault
- Use secure file permissions (0640) for .env files
- Regularly rotate API tokens
- Monitor container logs for security events

## Supported Platforms

- Ubuntu 20.04+ (Focal, Jammy, Noble)
- Debian 11+ (Bullseye, Bookworm)
- RHEL/CentOS 8+

## Example Usage

```yaml
- hosts: servers
  become: true
  vars:
    remnanode_remnawave_panel_domain: "panel.example.com"
    remnanode_remnawave_api_token: "{{ vault_api_token }}"
    remnanode_node:
      register: true
      country_code: "US"
      profile:
        name: "production_profile"
        inbounds:
          - tag: "main-inbound"
  roles:
          - lagomvpn.xray.remnanode
``` 