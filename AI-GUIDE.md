# AI Guide: OpenClaw Gateway Autostart Setup

This document helps AI assistants set up OpenClaw Gateway autostart for users.

## What This Project Does

Sets up OpenClaw Gateway to automatically start when the user logs in:

- **Cross-platform**: Windows, macOS, Linux
- **Hidden window**: No terminal popup
- **Optional proxy check**: Wait for proxy before starting (for users behind firewall)
- **Process cleanup**: Avoid port conflicts
- **Logging**: Troubleshooting support

## Quick Decision Tree

```
User's OS?
├── Windows → setup-windows.ps1
├── macOS   → setup-macos.sh
└── Linux   → setup-linux.sh

User needs proxy?
├── Yes (China, corporate network, etc.)
│   └── Use --use-proxy flag or -UseProxy $true
└── No (direct internet access)
    └── Default (no proxy check)
```

## Prerequisites

Before running setup, verify:

1. **OpenClaw CLI installed**: `npm install -g openclaw`
2. **OS-specific requirements**:
   - Windows: PowerShell 5.1+, Administrator access
   - macOS: launchd (built-in)
   - Linux: systemd (most distributions)

## Platform-Specific Setup

### Windows

```powershell
# Run as Administrator

# Without proxy (most users)
powershell -ExecutionPolicy Bypass -File setup-windows.ps1

# With proxy (users behind firewall)
powershell -ExecutionPolicy Bypass -File setup-windows.ps1 -UseProxy $true -ProxyPort "7890"
```

### macOS

```bash
# Without proxy
chmod +x setup-macos.sh
./setup-macos.sh

# With proxy
./setup-macos.sh --use-proxy --proxy-port 7890
```

### Linux

```bash
# Without proxy
chmod +x setup-linux.sh
./setup-linux.sh

# With proxy
./setup-linux.sh --use-proxy --proxy-port 7890
```

## Parameters

| Parameter | Windows | macOS/Linux | Default | Description |
|-----------|---------|-------------|---------|-------------|
| Use Proxy | `-UseProxy $true` | `--use-proxy` | false | Enable proxy check |
| Proxy Port | `-ProxyPort "7890"` | `--proxy-port 7890` | 7890 | HTTP proxy port |
| Proxy Wait | `-ProxyWaitSeconds 20` | `--proxy-wait 20` | 20 | Initial wait (seconds) |
| Proxy Retries | `-ProxyMaxRetries 8` | (fixed) | 8 | Check retry count |

## Common Proxy Ports

| Software | Default Port |
|----------|--------------|
| Clash | 7890 |
| V2Ray | 10808 |
| Shadowsocks | 1080 |
| Veee | 15236 |

## Verification Commands

### Windows
```powershell
Get-ScheduledTask -TaskName "OpenClaw Gateway"
netstat -ano | findstr "18789"
```

### macOS
```bash
launchctl list | grep openclaw
lsof -i :18789
```

### Linux
```bash
systemctl --user status openclaw-gateway
ss -tlnp | grep 18789
```

## Troubleshooting

### Gateway not starting

1. Check service/task status
2. Check logs: `~/.openclaw/logs/gateway-YYYY-MM-DD.log`
3. Run launcher script manually to see errors

### Port conflict

```bash
# Find process using port
# Windows
netstat -ano | findstr "18789"

# macOS/Linux
lsof -i :18789

# Kill process
kill -9 <PID>
```

### Proxy not working

1. Verify proxy software is running
2. Check proxy port is correct
3. Increase wait time

## Files Created

### Windows
- `~/.openclaw/gateway-hidden.vbs` - Hidden launcher
- `~/.openclaw/gateway.cmd` - Gateway command
- Task Scheduler: "OpenClaw Gateway"

### macOS
- `~/.openclaw/gateway-launcher.sh` - Launcher script
- `~/Library/LaunchAgents/com.openclaw.gateway.plist` - LaunchAgent

### Linux
- `~/.openclaw/gateway-launcher.sh` - Launcher script
- `~/.config/systemd/user/openclaw-gateway.service` - Systemd service

## Uninstall

### Windows
```powershell
Unregister-ScheduledTask -TaskName "OpenClaw Gateway" -Confirm:$false
Remove-Item "$env:USERPROFILE\.openclaw\gateway-hidden.vbs" -Force
```

### macOS
```bash
launchctl unload ~/Library/LaunchAgents/com.openclaw.gateway.plist
rm ~/Library/LaunchAgents/com.openclaw.gateway.plist
```

### Linux
```bash
systemctl --user stop openclaw-gateway
systemctl --user disable openclaw-gateway
rm ~/.config/systemd/user/openclaw-gateway.service
```
