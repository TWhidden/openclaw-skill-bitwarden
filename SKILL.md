---
name: bitwarden
description: Bitwarden password manager integration. Use when storing, retrieving, generating, or managing passwords and credentials. Wraps the Bitwarden CLI (bw) with automatic session management. Works with both official Bitwarden and Vaultwarden servers.
---

# Bitwarden

Bitwarden CLI (`bw`) wrapper with automatic login, session caching, and convenient commands.

## Requirements

- Bitwarden CLI (`bw`) installed: `npm install -g @bitwarden/cli`
- A Bitwarden or Vaultwarden server instance
- Credentials configured (see Configuration below)

## Configuration

Set credentials via environment variables or a credentials file:

```bash
# Environment variables (preferred)
export BW_SERVER="https://vault.bitwarden.com"  # Official Bitwarden
# OR
export BW_SERVER="https://your-vaultwarden-instance.example.com"  # Vaultwarden
export BW_EMAIL="your-email@example.com"
export BW_MASTER_PASSWORD="your-master-password"

# Or use a credentials file (default: secrets/bitwarden.env)
export CREDS_FILE="/path/to/your/bitwarden.env"
```

The credentials file should contain:

```
BW_SERVER=https://vault.bitwarden.com
BW_EMAIL=your-email@example.com
BW_MASTER_PASSWORD=your-master-password
```

## Invocation

```bash
bash skills/bitwarden/bw.sh <command> [args...]
```

## Commands

| Command | Description | Example |
|---------|-------------|---------|
| `login` | Login & unlock vault | `bw.sh login` |
| `status` | Show vault status | `bw.sh status` |
| `list [search]` | List/search items | `bw.sh list github` |
| `get <name\|id>` | Get full item JSON | `bw.sh get "GitHub"` |
| `get-password <name\|id>` | Get password only | `bw.sh get-password "GitHub"` |
| `get-username <name\|id>` | Get username only | `bw.sh get-username "GitHub"` |
| `create <name> <user> <pass> [uri] [notes]` | Create login | `bw.sh create "GitHub" user pass https://github.com` |
| `generate [length]` | Generate password | `bw.sh generate 32` |
| `delete <id>` | Delete item | `bw.sh delete <uuid>` |
| `lock` | Lock vault | `bw.sh lock` |

## Workflow

1. First call per session: `bw.sh login` (auto-authenticates from configured credentials)
2. Session token cached at `/tmp/.bw_session`
3. All subsequent commands auto-use the cached session
4. After reboot/restart: run `login` again

## Storing New Credentials

```bash
# Generate + store
PASS=$(bash skills/bitwarden/bw.sh generate 32)
bash skills/bitwarden/bw.sh create "New Service" "user@email.com" "$PASS" "https://service.com"
```

## Guardrails

- Never paste secrets into logs, chat, or code.
- Keep `bitwarden.env` out of version control.
- Use `chmod 600` on credential files.
- Session tokens are stored in `/tmp` and cleared on lock/logout.
