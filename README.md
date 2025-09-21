# Claude Code Docker DevContainer

A secure, sandboxed Docker DevContainer environment for running Claude Code CLI with network isolation and development tools.

## Features

- **Network Security**: Restricts outbound traffic to only essential domains (GitHub, npm, Anthropic APIs)
- **Development Ready**: Includes git, zsh, fzf, GitHub CLI, and other essential tools
- **Persistent Storage**: Maintains command history and Claude configuration across sessions
- **Terminal Customization**: Configured with Zsh and Powerline10k theme
- **VS Code Integration**: Full DevContainer support with Claude Code extension

## Prerequisites

### Installing Docker Desktop

Before using this DevContainer, you need to install Docker Desktop:

#### Windows
1. Download Docker Desktop from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Run the installer and follow the setup wizard
3. Restart your computer when prompted
4. Launch Docker Desktop and wait for it to start

#### macOS
1. Download Docker Desktop from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
2. Open the .dmg file and drag Docker to Applications
3. Launch Docker from Applications
4. Follow the setup instructions

#### Linux
1. Follow the official installation guide for your distribution at [docs.docker.com/desktop/install/linux-install](https://docs.docker.com/desktop/install/linux-install)
2. Start Docker Desktop from your application menu

### Other Requirements

- VS Code with DevContainer extension (optional but recommended)
- Windows PowerShell (for Windows users using the automation script)

## Quick Start

### Option 1: Using VS Code DevContainers

1. Clone this repository
2. Open in VS Code
3. When prompted, click "Reopen in Container"
4. Wait for the container to build and start
5. Open a terminal and run `claude` to start Claude Code CLI

### Option 2: Using PowerShell Script (Windows)

```powershell
# With Docker
./run-claude-docker.ps1 -Backend docker

# With Podman
./run-claude-docker.ps1 -Backend podman
```

### Option 3: Using DevContainer CLI

```bash
# Install devcontainer CLI if needed
npm install -g @devcontainers/cli

# Start the container
devcontainer up --workspace-folder .

# Connect to the container
docker exec -it $(docker ps -q --filter label=devcontainer.local_folder=$(pwd)) zsh
```

## Network Security

The container implements strict firewall rules using iptables and ipset, allowing connections only to:

- **Development Services**:
  - GitHub API and repositories
  - npm registry
  - Anthropic API endpoints
  - VS Code marketplace and update servers

- **Local Communication**:
  - DNS resolution (port 53)
  - SSH connections (port 22)
  - Host network access
  - Localhost/loopback

All other outbound traffic is blocked to prevent unauthorized network access.

## Container Structure

```
.
├── Dockerfile              # Container image definition
├── devcontainer.json       # VS Code DevContainer configuration
├── init-firewall.sh        # Network isolation setup script
├── run-claude-docker.ps1   # Windows automation script
└── CLAUDE.md              # Claude Code guidance file
```

## Configuration

### Environment Variables

- `TZ`: Timezone (default: America/Los_Angeles)
- `CLAUDE_CODE_VERSION`: Claude Code CLI version (default: latest)
- `NODE_OPTIONS`: Node.js memory settings (default: 4GB max)

### Persistent Volumes

- `/commandhistory`: Bash/Zsh history persistence
- `/home/node/.claude`: Claude configuration persistence

### Installed Tools

- **Runtime**: Node.js 20, npm
- **CLI Tools**: Claude Code, GitHub CLI (gh), git
- **Editors**: vim, nano
- **Shell**: zsh with Oh My Zsh and Powerline10k
- **Utilities**: fzf, jq, git-delta, aggregate
- **Network**: iptables, ipset, dnsutils

## Security Notes

This container is designed with security in mind:

1. **Network Isolation**: Firewall rules prevent unauthorized outbound connections
2. **Non-root User**: Runs as `node` user with limited privileges
3. **Minimal Attack Surface**: Only essential tools and services included
4. **Verification**: Firewall rules are verified on startup

## Troubleshooting

### Container won't start
- Ensure Docker/Podman is running
- Check that ports are not already in use
- Verify you have sufficient disk space

### Network connectivity issues
- The firewall blocks most external connections by design
- Check `sudo iptables -L -n -v` to see current rules
- Verify allowed domains with `sudo ipset list allowed-domains`

### Permission errors
- The container runs as the `node` user
- Use `sudo` for administrative commands
- Firewall script requires sudo (configured in sudoers)

## License

This project is provided as-is for use with Claude Code CLI. Please refer to Anthropic's terms of service for Claude Code usage.