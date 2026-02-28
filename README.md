# OpenClaw Gateway Autostart

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-blue.svg)](https://github.com/veil-chow-fyaic/OpenClaw-Gateway-Autostart)
[![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-purple.svg)](https://docs.microsoft.com/powershell/)

> 跨平台 OpenClaw Gateway 开机自启动方案，支持隐藏窗口、代理健康检查、进程管理。

## ✨ 功能特性

| 特性 | Windows | macOS | Linux |
|------|:-------:|:-----:|:-----:|
| 开机自启 | ✅ | ✅ | ✅ |
| 隐藏窗口 | ✅ | ✅ | ✅ |
| 代理检测 | ✅ | ✅ | ✅ |
| 进程清理 | ✅ | ✅ | ✅ |
| 日志记录 | ✅ | ✅ | ✅ |

## 📋 前置要求

- **OpenClaw CLI**: `npm install -g openclaw`
- **Windows**: PowerShell 5.1+
- **macOS**: launchd (系统自带)
- **Linux**: systemd (大多数发行版自带)

## 🚀 快速开始

### Windows

```powershell
# 克隆仓库
git clone https://github.com/veil-chow-fyaic/OpenClaw-Gateway-Autostart.git
cd OpenClaw-Gateway-Autostart

# 运行安装脚本（管理员权限）
powershell -ExecutionPolicy Bypass -File setup-windows.ps1
```

### macOS

```bash
# 克隆仓库
git clone https://github.com/veil-chow-fyaic/OpenClaw-Gateway-Autostart.git
cd OpenClaw-Gateway-Autostart

# 运行安装脚本
chmod +x setup-macos.sh
./setup-macos.sh
```

### Linux

```bash
# 克隆仓库
git clone https://github.com/veil-chow-fyaic/OpenClaw-Gateway-Autostart.git
cd OpenClaw-Gateway-Autostart

# 运行安装脚本
chmod +x setup-linux.sh
./setup-linux.sh
```

## ⚙️ 配置选项

### 通用参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| `USE_PROXY` | `false` | 是否启用代理检测 |
| `PROXY_PORT` | `7890` | HTTP 代理端口 |
| `PROXY_WAIT` | `20` | 代理初始等待时间（秒） |
| `GATEWAY_PORT` | `18789` | Gateway 端口 |

### Windows 参数

```powershell
powershell -ExecutionPolicy Bypass -File setup-windows.ps1 `
  -UseProxy $true `
  -ProxyPort "7890" `
  -ProxyWaitSeconds 20
```

### macOS / Linux 参数

```bash
# 编辑配置
./setup-macos.sh --use-proxy --proxy-port 7890
./setup-linux.sh --use-proxy --proxy-port 7890
```

## 🔧 使用场景

### 场景 1：直连网络（无需代理）

大多数用户，网络可直接访问 Telegram：

```powershell
# Windows
powershell -ExecutionPolicy Bypass -File setup-windows.ps1 -UseProxy $false
```

```bash
# macOS / Linux
./setup-macos.sh   # 默认不使用代理
./setup-linux.sh
```

### 场景 2：需要代理（中国大陆等）

网络需要代理才能访问 Telegram：

```powershell
# Windows - 使用 Veee/Clash 等代理
powershell -ExecutionPolicy Bypass -File setup-windows.ps1 `
  -UseProxy $true `
  -ProxyPort "7890"
```

```bash
# macOS / Linux
./setup-macos.sh --use-proxy --proxy-port 7890
```

### 场景 3：自定义代理配置

如果你的代理需要认证或使用 SOCKS：

1. 编辑生成的配置文件
2. 修改代理设置

**Windows**: `~/.openclaw/gateway-hidden.vbs`
**macOS**: `~/.openclaw/gateway-launcher.sh`
**Linux**: `~/.openclaw/gateway-launcher.sh`

## 📁 项目结构

```
OpenClaw-Gateway-Autostart/
├── README.md                 # 本文档
├── AI-GUIDE.md              # AI 助手指南
├── LICENSE                  # MIT 许可证
├── setup-windows.ps1        # Windows 安装脚本
├── setup-macos.sh           # macOS 安装脚本
├── setup-linux.sh           # Linux 安装脚本
└── templates/
    ├── gateway-hidden.vbs   # Windows 隐藏启动器模板
    └── gateway-launcher.sh  # Unix 启动器模板
```

## 📂 安装后文件位置

### Windows

```
~/.openclaw/
├── gateway-hidden.vbs       # 隐藏启动器
├── gateway.cmd              # Gateway 命令
└── logs/
    └── gateway-*.log        # 日志文件

任务计划: "OpenClaw Gateway"
```

### macOS

```
~/.openclaw/
├── gateway-launcher.sh      # 启动脚本
└── logs/
    └── gateway-*.log

LaunchAgent: ~/Library/LaunchAgents/com.openclaw.gateway.plist
```

### Linux

```
~/.openclaw/
├── gateway-launcher.sh      # 启动脚本
└── logs/
    └── gateway-*.log

Systemd: ~/.config/systemd/user/openclaw-gateway.service
```

## 🔍 验证与控制

### Windows

```powershell
# 检查状态
Get-ScheduledTask -TaskName "OpenClaw Gateway"

# 手动启动
Start-ScheduledTask -TaskName "OpenClaw Gateway"

# 停止
schtasks /End /TN "OpenClaw Gateway"

# 检查端口
netstat -ano | findstr "18789"
```

### macOS

```bash
# 检查状态
launchctl list | grep openclaw

# 手动启动
launchctl start com.openclaw.gateway

# 停止
launchctl stop com.openclaw.gateway

# 检查端口
lsof -i :18789
```

### Linux

```bash
# 检查状态
systemctl --user status openclaw-gateway

# 手动启动
systemctl --user start openclaw-gateway

# 停止
systemctl --user stop openclaw-gateway

# 检查端口
ss -tlnp | grep 18789
```

## 🐛 故障排查

<details>
<summary>Windows 故障排查</summary>

### Gateway 未启动

```powershell
# 1. 检查任务计划
Get-ScheduledTask -TaskName "OpenClaw Gateway"

# 2. 查看日志
Get-Content "$env:USERPROFILE\.openclaw\logs\gateway-$(Get-Date -Format 'yyyy-MM-dd').log"

# 3. 手动测试
wscript "$env:USERPROFILE\.openclaw\gateway-hidden.vbs"
```

### 端口被占用

```powershell
netstat -ano | findstr "18789"
taskkill /PID <PID> /F
```

### 代理连接失败

1. 确认代理软件已启动
2. 检查代理端口是否正确
3. 增加等待时间: `-ProxyWaitSeconds 30`

</details>

<details>
<summary>macOS 故障排查</summary>

### Gateway 未启动

```bash
# 1. 检查 LaunchAgent
launchctl list | grep openclaw

# 2. 查看日志
cat ~/.openclaw/logs/gateway-$(date +%Y-%m-%d).log

# 3. 手动测试
~/.openclaw/gateway-launcher.sh
```

### 权限问题

```bash
chmod +x ~/.openclaw/gateway-launcher.sh
```

</details>

<details>
<summary>Linux 故障排查</summary>

### Gateway 未启动

```bash
# 1. 检查服务状态
systemctl --user status openclaw-gateway

# 2. 查看日志
journalctl --user -u openclaw-gateway

# 3. 查看文件日志
cat ~/.openclaw/logs/gateway-$(date +%Y-%m-%d).log
```

### 服务未自动启动

```bash
# 启用用户服务自动启动
systemctl --user enable openclaw-gateway

# 允许用户服务在登录前运行
sudo loginctl enable-linger $USER
```

</details>

## 🗑️ 卸载

### Windows

```powershell
Unregister-ScheduledTask -TaskName "OpenClaw Gateway" -Confirm:$false
Remove-Item "$env:USERPROFILE\.openclaw\gateway-hidden.vbs" -Force -ErrorAction SilentlyContinue
Remove-Item "$env:USERPROFILE\.openclaw\gateway.cmd" -Force -ErrorAction SilentlyContinue
```

### macOS

```bash
launchctl unload ~/Library/LaunchAgents/com.openclaw.gateway.plist
rm ~/Library/LaunchAgents/com.openclaw.gateway.plist
rm -rf ~/.openclaw
```

### Linux

```bash
systemctl --user stop openclaw-gateway
systemctl --user disable openclaw-gateway
rm ~/.config/systemd/user/openclaw-gateway.service
rm -rf ~/.openclaw
```

## 🤝 给 AI 的提示

如果你想让 AI 帮忙设置，请让 AI 阅读 [AI-GUIDE.md](./AI-GUIDE.md)。

## ❓ 常见问题

<details>
<summary>我需要代理吗？</summary>

- **不需要**：如果你在中国大陆以外，或网络可直接访问 Telegram
- **需要**：如果你在中国大陆，或网络需要代理才能访问外网

常见的代理软件端口：
- Clash: `7890`
- Veee: `15236`
- V2Ray: `10808`
- Shadowsocks: `1080`

</details>

<details>
<summary>如何确认 Gateway 是否正常运行？</summary>

访问 http://localhost:18789 如果返回 JSON 响应，说明 Gateway 正常运行。

</details>

<details>
<summary>为什么开机后 Gateway 没有启动？</summary>

1. 检查安装脚本是否执行成功
2. 查看日志文件中的错误信息
3. 如果使用代理，确保代理软件已配置开机自启

</details>

## 📄 许可证

[MIT License](./LICENSE)

## 🙏 致谢

- [OpenClaw](https://www.npmjs.com/package/openclaw) - AI 助手 CLI 工具
