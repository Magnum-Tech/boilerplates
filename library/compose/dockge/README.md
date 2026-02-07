# Dockge - Docker Compose Stack Manager

A self-hosted, stack-oriented manager for Docker Compose with a focus on managing services through their `compose.yaml` files. Dockge provides a clean web UI for creating, editing, deploying, and managing Docker Compose stacks.

## Features

- **Compose-First Workflow**: Manage stacks directly through their `compose.yaml` files
- **Full Stack Management**: Create, edit, start, stop, restart, and delete stacks
- **Real-Time Logs**: View container logs directly in the UI
- **Terminal Access**: Execute commands in running containers
- **Multi-Stack Support**: Manage multiple Docker Compose projects from one interface
- **File Editor**: Built-in YAML editor with syntax highlighting

## Prerequisites

- Docker Engine installed and running
- Docker Compose V2
- A reverse proxy network (if using Traefik integration)
- Sufficient permissions to access Docker socket

## Usage

### Generate Template

```bash
# Interactive mode
boilerplates compose generate dockge

# With custom output directory
boilerplates compose generate dockge ./my-dockge

# Non-interactive with variables
boilerplates compose generate dockge ./my-dockge \
  --var service_name=dockge \
  --var host_port=5001 \
  --var host_stacks_dir=/opt/stacks \
  --var traefik_enabled=true \
  --var traefik_preset=internal-only \
  --no-interactive
```

### Deploy

```bash
cd ./my-dockge
docker compose up -d
```

### Access

- **Web UI**: `http://<host>:<port>` (default: http://localhost:5001)
- **With Traefik**: Access via configured domain based on your preset

## Configuration Options

### General Settings

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `service_name` | string | `dockge` | Service and stack name |
| `service_host` | string | `dockge` | Container name |
| `service_image` | string | `louislam/dockge` | Docker image |
| `service_version` | string | `latest` | Image tag/version |

### Network & Ports

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `host_port` | integer | `5001` | Host port for web UI |
| `service_port` | integer | `5001` | Container port |
| `network_proxy` | string | `proxy` | Docker network for reverse proxy |

### Storage

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `host_config_dir` | string | `./data` | Host directory for Dockge data |
| `host_stacks_dir` | string | `/opt/stacks` | Host directory for Docker stacks |
| `dockge_stacks_dir` | string | `/opt/stacks` | Container path for stacks |

**Important**: `host_stacks_dir` and `dockge_stacks_dir` must point to the same location!

### Homepage Integration

Enable Homepage dashboard integration:

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `homepage_enabled` | boolean | `false` | Enable Homepage integration |
| `homepage_group` | string | `Docker Management` | Homepage group |
| `homepage_name` | string | `Dockge` | Display name |
| `homepage_icon` | string | `docker.png` | Icon filename |
| `homepage_href` | string | `http://localhost:5001` | Service URL |
| `homepage_description` | string | `Docker Compose Stack Manager` | Description |

### Traefik Integration

Configure reverse proxy routing:

| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `traefik_enabled` | boolean | `true` | Enable Traefik integration |
| `traefik_preset` | enum | `internal-only` | Domain routing preset |
| `service_subdomain` | string | `dockge` | Subdomain prefix |
| `host_name` | string | `""` | Optional host identifier suffix |

**Traefik Presets:**

- `disabled`: No Traefik routing
- `internal-only`: Local + Yggdrasil domains (recommended for management tools)
- `external-only`: Public business domains only
- `personal-only`: Internal + personal family domains
- `business-only`: Business domains only
- `all-domains`: All configured domains

### Domain Configuration

Customize domain names for each routing context:

| Variable | Default | Description |
|----------|---------|-------------|
| `domain_root_local` | `local` | Local network domain |
| `domain_root_yggdrasil` | `yggdrasil.lan` | Yggdrasil mesh domain |
| `domain_root_magnumtech` | `local.magnumtech.live` | MagnumTech domain |
| `domain_root_magnumtek` | `local.magnumtek.com` | MagnumTek domain |
| `domain_root_ourjones` | `homelab.ourjones.family` | Personal family domain |
| `domain_root_householderp` | `local.householderp.com` | HouseholderP domain |
| `domain_root_tracksoft` | `local.tracksoft.solutions` | TrackSoft domain |

## Post-Installation

1. **Initial Setup**:
   - Access the web UI at your configured URL
   - The first run will initialize the application
   - Create your first stack using the UI

2. **Stack Directory**:
   - All stacks are stored in `host_stacks_dir`
   - Each stack has its own directory with a `compose.yaml` file
   - You can also manage stacks via filesystem if needed

3. **Docker Socket Access**:
   - Dockge requires access to `/var/run/docker.sock`
   - This gives full control over Docker on the host
   - **Security Note**: Only use in trusted environments

4. **Backup**:
   - Back up `host_config_dir` for app configuration
   - Back up `host_stacks_dir` for all your compose stacks

## Security Considerations

- **Docker Socket Access**: Grants full Docker control - use carefully
- **Network Isolation**: Consider using `internal-only` preset for management UIs
- **Authentication**: Configure appropriate access controls
- **TLS**: Enable HTTPS through Traefik for remote access

## Troubleshooting

### Port Already in Use

```bash
# Check what's using the port
sudo lsof -i :5001

# Change the port in your configuration
boilerplates compose generate dockge --var host_port=5002
```

### Stack Directory Issues

Ensure the directory permissions allow Docker to access it:

```bash
sudo mkdir -p /opt/stacks
sudo chown -R $USER:$USER /opt/stacks
sudo chmod -R 755 /opt/stacks
```

### Can't Connect to Docker

Verify Docker socket permissions:

```bash
ls -la /var/run/docker.sock
# Should show: srw-rw---- root docker

# Add user to docker group if needed
sudo usermod -aG docker $USER
# Log out and back in for changes to take effect
```

## Resources

- **Official Repository**: https://github.com/louislam/dockge
- **Documentation**: https://github.com/louislam/dockge/wiki
- **Community**: GitHub Issues and Discussions

## Comparison with Portainer

| Feature | Dockge | Portainer |
|---------|--------|-----------|
| **Focus** | Docker Compose stacks | Comprehensive container management |
| **UI Philosophy** | Minimal, compose-first | Feature-rich dashboard |
| **Configuration** | Direct YAML editing | GUI-driven |
| **Resource Usage** | Lightweight | More resource-intensive |
| **Best For** | Compose enthusiasts | General container management |

## License

This template is provided as-is for use with the boilerplates CLI tool.
Dockge itself is licensed under the MIT License.
