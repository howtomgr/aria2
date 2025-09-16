# aria2 Installation Guide

aria2 is a free and open-source download utility. Aria2 supports HTTP/S, FTP, SFTP, BitTorrent, and Metalink downloads

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
  - RAM: 128MB minimum
  - Storage: 1GB for downloads
  - Network: Multiple protocols
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 6800 (default aria2 port)
  - RPC port 6800
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

# Install aria2
sudo dnf install -y aria2

# Enable and start service
sudo systemctl enable --now aria2

# Configure firewall
sudo firewall-cmd --permanent --add-port=6800/tcp
sudo firewall-cmd --reload

# Verify installation
aria2 --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install aria2
sudo apt install -y aria2

# Enable and start service
sudo systemctl enable --now aria2

# Configure firewall
sudo ufw allow 6800

# Verify installation
aria2 --version
```

### Arch Linux

```bash
# Install aria2
sudo pacman -S aria2

# Enable and start service
sudo systemctl enable --now aria2

# Verify installation
aria2 --version
```

### Alpine Linux

```bash
# Install aria2
apk add --no-cache aria2

# Enable and start service
rc-update add aria2 default
rc-service aria2 start

# Verify installation
aria2 --version
```

### openSUSE/SLES

```bash
# Install aria2
sudo zypper install -y aria2

# Enable and start service
sudo systemctl enable --now aria2

# Configure firewall
sudo firewall-cmd --permanent --add-port=6800/tcp
sudo firewall-cmd --reload

# Verify installation
aria2 --version
```

### macOS

```bash
# Using Homebrew
brew install aria2

# Start service
brew services start aria2

# Verify installation
aria2 --version
```

### FreeBSD

```bash
# Using pkg
pkg install aria2

# Enable in rc.conf
echo 'aria2_enable="YES"' >> /etc/rc.conf

# Start service
service aria2 start

# Verify installation
aria2 --version
```

### Windows

```bash
# Using Chocolatey
choco install aria2

# Or using Scoop
scoop install aria2

# Verify installation
aria2 --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/aria2

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
aria2 --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable aria2

# Start service
sudo systemctl start aria2

# Stop service
sudo systemctl stop aria2

# Restart service
sudo systemctl restart aria2

# Check status
sudo systemctl status aria2

# View logs
sudo journalctl -u aria2 -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add aria2 default

# Start service
rc-service aria2 start

# Stop service
rc-service aria2 stop

# Restart service
rc-service aria2 restart

# Check status
rc-service aria2 status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'aria2_enable="YES"' >> /etc/rc.conf

# Start service
service aria2 start

# Stop service
service aria2 stop

# Restart service
service aria2 restart

# Check status
service aria2 status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start aria2
brew services stop aria2
brew services restart aria2

# Check status
brew services list | grep aria2
```

### Windows Service Manager

```powershell
# Start service
net start aria2

# Stop service
net stop aria2

# Using PowerShell
Start-Service aria2
Stop-Service aria2
Restart-Service aria2

# Check status
Get-Service aria2
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream aria2_backend {
    server 127.0.0.1:6800;
}

server {
    listen 80;
    server_name aria2.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name aria2.example.com;

    ssl_certificate /etc/ssl/certs/aria2.example.com.crt;
    ssl_certificate_key /etc/ssl/private/aria2.example.com.key;

    location / {
        proxy_pass http://aria2_backend;
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
    ServerName aria2.example.com
    Redirect permanent / https://aria2.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName aria2.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/aria2.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/aria2.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:6800/
    ProxyPassReverse / http://127.0.0.1:6800/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend aria2_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/aria2.pem
    redirect scheme https if !{ ssl_fc }
    default_backend aria2_backend

backend aria2_backend
    balance roundrobin
    server aria21 127.0.0.1:6800 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R aria2:aria2 /etc/aria2
sudo chmod 750 /etc/aria2

# Configure firewall
sudo firewall-cmd --permanent --add-port=6800/tcp
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
sudo systemctl status aria2

# View logs
sudo journalctl -u aria2 -f

# Monitor resource usage
top -p $(pgrep aria2)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/aria2"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/aria2-backup-$DATE.tar.gz" /etc/aria2 /var/lib/aria2

echo "Backup completed: $BACKUP_DIR/aria2-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop aria2

# Restore from backup
tar -xzf /backup/aria2/aria2-backup-*.tar.gz -C /

# Start service
sudo systemctl start aria2
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u aria2 -n 100
sudo tail -f /var/log/aria2/aria2.log

# Check configuration
aria2 --version

# Check permissions
ls -la /etc/aria2
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 6800

# Test connectivity
telnet localhost 6800

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep aria2)

# Check disk I/O
iotop -p $(pgrep aria2)

# Check connections
ss -an | grep 6800
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  aria2:
    image: aria2:latest
    ports:
      - "6800:6800"
    volumes:
      - ./config:/etc/aria2
      - ./data:/var/lib/aria2
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update aria2

# Debian/Ubuntu
sudo apt update && sudo apt upgrade aria2

# Arch Linux
sudo pacman -Syu aria2

# Alpine Linux
apk update && apk upgrade aria2

# openSUSE
sudo zypper update aria2

# FreeBSD
pkg update && pkg upgrade aria2

# Always backup before updates
tar -czf /backup/aria2-pre-update-$(date +%Y%m%d).tar.gz /etc/aria2

# Restart after updates
sudo systemctl restart aria2
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/aria2

# Clean old logs
find /var/log/aria2 -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/aria2
```

## Additional Resources

- Official Documentation: https://docs.aria2.org/
- GitHub Repository: https://github.com/aria2/aria2
- Community Forum: https://forum.aria2.org/
- Best Practices Guide: https://docs.aria2.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
