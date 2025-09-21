# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Docker DevContainer setup for running Claude Code CLI in an isolated, sandboxed environment with network firewall restrictions. The container includes development tools and restricts network access to only necessary domains (GitHub, npm registry, Anthropic APIs, VS Code marketplace).

## Architecture

- **Dockerfile**: Builds a Node.js 20 container with Claude Code CLI, development tools, and networking utilities (iptables/ipset)
- **devcontainer.json**: VS Code DevContainer configuration with Claude Code extension and terminal customization
- **init-firewall.sh**: Network isolation script that restricts outbound traffic to allowed domains using iptables and ipset
- **run-claude-docker.ps1**: Windows PowerShell script for automating DevContainer startup with Docker or Podman

## Key Commands

### Container Management
```bash
# Build the container (from host)
docker build -t claude-code-sandbox .

# Start DevContainer (from host, Windows)
./run-claude-docker.ps1 -Backend docker  # or podman

# Start DevContainer (from host, using devcontainer CLI)
devcontainer up --workspace-folder .
```

### Inside Container
```bash
# Run Claude Code CLI
claude

# Verify firewall rules
sudo iptables -L -n -v
sudo ipset list allowed-domains
```

## Network Security

The container uses iptables firewall rules to restrict network access. Only the following are allowed:
- DNS resolution (port 53)
- SSH connections (port 22)
- Host network communication
- Specific allowed domains: GitHub, npm registry, Anthropic APIs, VS Code marketplace

All other outbound traffic is blocked. The firewall is initialized on container startup via `postStartCommand`.

## Container Features

- Node.js 20 runtime with npm
- Claude Code CLI installed globally
- Development tools: git, zsh, fzf, vim, nano, jq
- GitHub CLI (gh) for repository operations
- Git delta for improved diffs
- Persistent bash/zsh history and Claude configuration via volume mounts