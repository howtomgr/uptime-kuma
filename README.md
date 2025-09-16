# uptime-kuma Installation Guide

uptime-kuma is a free and open-source self-hosted monitoring tool. Uptime Kuma provides beautiful status pages and monitoring with notifications, serving as an alternative to Pingdom, UptimeRobot, or StatusPage.io

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 200MB for application
  - Network: HTTP/HTTPS for checks
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3001 (default uptime-kuma port)
  - WebSocket support
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install uptime-kuma
sudo dnf install -y uptime-kuma

# Enable and start service
sudo systemctl enable --now uptime-kuma

# Configure firewall
sudo firewall-cmd --permanent --add-port=3001/tcp
sudo firewall-cmd --reload

# Verify installation
node server/server.js --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install uptime-kuma
sudo apt install -y uptime-kuma

# Enable and start service
sudo systemctl enable --now uptime-kuma

# Configure firewall
sudo ufw allow 3001

# Verify installation
node server/server.js --version
```

### Arch Linux

```bash
# Install uptime-kuma
sudo pacman -S uptime-kuma

# Enable and start service
sudo systemctl enable --now uptime-kuma

# Verify installation
node server/server.js --version
```

### Alpine Linux

```bash
# Install uptime-kuma
apk add --no-cache uptime-kuma

# Enable and start service
rc-update add uptime-kuma default
rc-service uptime-kuma start

# Verify installation
node server/server.js --version
```

### openSUSE/SLES

```bash
# Install uptime-kuma
sudo zypper install -y uptime-kuma

# Enable and start service
sudo systemctl enable --now uptime-kuma

# Configure firewall
sudo firewall-cmd --permanent --add-port=3001/tcp
sudo firewall-cmd --reload

# Verify installation
node server/server.js --version
```

### macOS

```bash
# Using Homebrew
brew install uptime-kuma

# Start service
brew services start uptime-kuma

# Verify installation
node server/server.js --version
```

### FreeBSD

```bash
# Using pkg
pkg install uptime-kuma

# Enable in rc.conf
echo 'uptime-kuma_enable="YES"' >> /etc/rc.conf

# Start service
service uptime-kuma start

# Verify installation
node server/server.js --version
```

### Windows

```bash
# Using Chocolatey
choco install uptime-kuma

# Or using Scoop
scoop install uptime-kuma

# Verify installation
node server/server.js --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/uptime-kuma

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
node server/server.js --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable uptime-kuma

# Start service
sudo systemctl start uptime-kuma

# Stop service
sudo systemctl stop uptime-kuma

# Restart service
sudo systemctl restart uptime-kuma

# Check status
sudo systemctl status uptime-kuma

# View logs
sudo journalctl -u uptime-kuma -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add uptime-kuma default

# Start service
rc-service uptime-kuma start

# Stop service
rc-service uptime-kuma stop

# Restart service
rc-service uptime-kuma restart

# Check status
rc-service uptime-kuma status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'uptime-kuma_enable="YES"' >> /etc/rc.conf

# Start service
service uptime-kuma start

# Stop service
service uptime-kuma stop

# Restart service
service uptime-kuma restart

# Check status
service uptime-kuma status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start uptime-kuma
brew services stop uptime-kuma
brew services restart uptime-kuma

# Check status
brew services list | grep uptime-kuma
```

### Windows Service Manager

```powershell
# Start service
net start uptime-kuma

# Stop service
net stop uptime-kuma

# Using PowerShell
Start-Service uptime-kuma
Stop-Service uptime-kuma
Restart-Service uptime-kuma

# Check status
Get-Service uptime-kuma
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream uptime-kuma_backend {
    server 127.0.0.1:3001;
}

server {
    listen 80;
    server_name uptime-kuma.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name uptime-kuma.example.com;

    ssl_certificate /etc/ssl/certs/uptime-kuma.example.com.crt;
    ssl_certificate_key /etc/ssl/private/uptime-kuma.example.com.key;

    location / {
        proxy_pass http://uptime-kuma_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName uptime-kuma.example.com
    Redirect permanent / https://uptime-kuma.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName uptime-kuma.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/uptime-kuma.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/uptime-kuma.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3001/
    ProxyPassReverse / http://127.0.0.1:3001/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend uptime-kuma_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/uptime-kuma.pem
    redirect scheme https if !{ ssl_fc }
    default_backend uptime-kuma_backend

backend uptime-kuma_backend
    balance roundrobin
    server uptime-kuma1 127.0.0.1:3001 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R uptime-kuma:uptime-kuma /etc/uptime-kuma
sudo chmod 750 /etc/uptime-kuma

# Configure firewall
sudo firewall-cmd --permanent --add-port=3001/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status uptime-kuma

# View logs
sudo journalctl -u uptime-kuma -f

# Monitor resource usage
top -p $(pgrep uptime-kuma)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/uptime-kuma"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/uptime-kuma-backup-$DATE.tar.gz" /etc/uptime-kuma /var/lib/uptime-kuma

echo "Backup completed: $BACKUP_DIR/uptime-kuma-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop uptime-kuma

# Restore from backup
tar -xzf /backup/uptime-kuma/uptime-kuma-backup-*.tar.gz -C /

# Start service
sudo systemctl start uptime-kuma
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u uptime-kuma -n 100
sudo tail -f /var/log/uptime-kuma/uptime-kuma.log

# Check configuration
node server/server.js --version

# Check permissions
ls -la /etc/uptime-kuma
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3001

# Test connectivity
telnet localhost 3001

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep uptime-kuma)

# Check disk I/O
iotop -p $(pgrep uptime-kuma)

# Check connections
ss -an | grep 3001
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  uptime-kuma:
    image: uptime-kuma:latest
    ports:
      - "3001:3001"
    volumes:
      - ./config:/etc/uptime-kuma
      - ./data:/var/lib/uptime-kuma
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update uptime-kuma

# Debian/Ubuntu
sudo apt update && sudo apt upgrade uptime-kuma

# Arch Linux
sudo pacman -Syu uptime-kuma

# Alpine Linux
apk update && apk upgrade uptime-kuma

# openSUSE
sudo zypper update uptime-kuma

# FreeBSD
pkg update && pkg upgrade uptime-kuma

# Always backup before updates
tar -czf /backup/uptime-kuma-pre-update-$(date +%Y%m%d).tar.gz /etc/uptime-kuma

# Restart after updates
sudo systemctl restart uptime-kuma
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/uptime-kuma

# Clean old logs
find /var/log/uptime-kuma -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/uptime-kuma
```

## Additional Resources

- Official Documentation: https://docs.uptime-kuma.org/
- GitHub Repository: https://github.com/uptime-kuma/uptime-kuma
- Community Forum: https://forum.uptime-kuma.org/
- Best Practices Guide: https://docs.uptime-kuma.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
