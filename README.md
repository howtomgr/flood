# flood Installation Guide

flood is a free and open-source modern torrent interface. Flood provides a modern web UI for various torrent clients

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
  - RAM: 256MB minimum
  - Storage: 100MB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3000 (default flood port)
  - None
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

# Install flood
sudo dnf install -y flood

# Enable and start service
sudo systemctl enable --now flood

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
flood --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install flood
sudo apt install -y flood

# Enable and start service
sudo systemctl enable --now flood

# Configure firewall
sudo ufw allow 3000

# Verify installation
flood --version
```

### Arch Linux

```bash
# Install flood
sudo pacman -S flood

# Enable and start service
sudo systemctl enable --now flood

# Verify installation
flood --version
```

### Alpine Linux

```bash
# Install flood
apk add --no-cache flood

# Enable and start service
rc-update add flood default
rc-service flood start

# Verify installation
flood --version
```

### openSUSE/SLES

```bash
# Install flood
sudo zypper install -y flood

# Enable and start service
sudo systemctl enable --now flood

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload

# Verify installation
flood --version
```

### macOS

```bash
# Using Homebrew
brew install flood

# Start service
brew services start flood

# Verify installation
flood --version
```

### FreeBSD

```bash
# Using pkg
pkg install flood

# Enable in rc.conf
echo 'flood_enable="YES"' >> /etc/rc.conf

# Start service
service flood start

# Verify installation
flood --version
```

### Windows

```bash
# Using Chocolatey
choco install flood

# Or using Scoop
scoop install flood

# Verify installation
flood --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/flood

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
flood --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable flood

# Start service
sudo systemctl start flood

# Stop service
sudo systemctl stop flood

# Restart service
sudo systemctl restart flood

# Check status
sudo systemctl status flood

# View logs
sudo journalctl -u flood -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add flood default

# Start service
rc-service flood start

# Stop service
rc-service flood stop

# Restart service
rc-service flood restart

# Check status
rc-service flood status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'flood_enable="YES"' >> /etc/rc.conf

# Start service
service flood start

# Stop service
service flood stop

# Restart service
service flood restart

# Check status
service flood status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start flood
brew services stop flood
brew services restart flood

# Check status
brew services list | grep flood
```

### Windows Service Manager

```powershell
# Start service
net start flood

# Stop service
net stop flood

# Using PowerShell
Start-Service flood
Stop-Service flood
Restart-Service flood

# Check status
Get-Service flood
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream flood_backend {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name flood.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name flood.example.com;

    ssl_certificate /etc/ssl/certs/flood.example.com.crt;
    ssl_certificate_key /etc/ssl/private/flood.example.com.key;

    location / {
        proxy_pass http://flood_backend;
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
    ServerName flood.example.com
    Redirect permanent / https://flood.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName flood.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/flood.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/flood.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3000/
    ProxyPassReverse / http://127.0.0.1:3000/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend flood_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/flood.pem
    redirect scheme https if !{ ssl_fc }
    default_backend flood_backend

backend flood_backend
    balance roundrobin
    server flood1 127.0.0.1:3000 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R flood:flood /etc/flood
sudo chmod 750 /etc/flood

# Configure firewall
sudo firewall-cmd --permanent --add-port=3000/tcp
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
sudo systemctl status flood

# View logs
sudo journalctl -u flood -f

# Monitor resource usage
top -p $(pgrep flood)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/flood"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/flood-backup-$DATE.tar.gz" /etc/flood /var/lib/flood

echo "Backup completed: $BACKUP_DIR/flood-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop flood

# Restore from backup
tar -xzf /backup/flood/flood-backup-*.tar.gz -C /

# Start service
sudo systemctl start flood
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u flood -n 100
sudo tail -f /var/log/flood/flood.log

# Check configuration
flood --version

# Check permissions
ls -la /etc/flood
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3000

# Test connectivity
telnet localhost 3000

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep flood)

# Check disk I/O
iotop -p $(pgrep flood)

# Check connections
ss -an | grep 3000
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  flood:
    image: flood:latest
    ports:
      - "3000:3000"
    volumes:
      - ./config:/etc/flood
      - ./data:/var/lib/flood
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update flood

# Debian/Ubuntu
sudo apt update && sudo apt upgrade flood

# Arch Linux
sudo pacman -Syu flood

# Alpine Linux
apk update && apk upgrade flood

# openSUSE
sudo zypper update flood

# FreeBSD
pkg update && pkg upgrade flood

# Always backup before updates
tar -czf /backup/flood-pre-update-$(date +%Y%m%d).tar.gz /etc/flood

# Restart after updates
sudo systemctl restart flood
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/flood

# Clean old logs
find /var/log/flood -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/flood
```

## Additional Resources

- Official Documentation: https://docs.flood.org/
- GitHub Repository: https://github.com/flood/flood
- Community Forum: https://forum.flood.org/
- Best Practices Guide: https://docs.flood.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
