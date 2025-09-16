# oscommerce Installation Guide

oscommerce is a free and open-source online shop. osCommerce provides online shop e-commerce solution

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
  - RAM: 1GB minimum
  - Storage: 5GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default oscommerce port)
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

# Install oscommerce
sudo dnf install -y oscommerce

# Enable and start service
sudo systemctl enable --now oscommerce

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
oscommerce --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install oscommerce
sudo apt install -y oscommerce

# Enable and start service
sudo systemctl enable --now oscommerce

# Configure firewall
sudo ufw allow 80

# Verify installation
oscommerce --version
```

### Arch Linux

```bash
# Install oscommerce
sudo pacman -S oscommerce

# Enable and start service
sudo systemctl enable --now oscommerce

# Verify installation
oscommerce --version
```

### Alpine Linux

```bash
# Install oscommerce
apk add --no-cache oscommerce

# Enable and start service
rc-update add oscommerce default
rc-service oscommerce start

# Verify installation
oscommerce --version
```

### openSUSE/SLES

```bash
# Install oscommerce
sudo zypper install -y oscommerce

# Enable and start service
sudo systemctl enable --now oscommerce

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
oscommerce --version
```

### macOS

```bash
# Using Homebrew
brew install oscommerce

# Start service
brew services start oscommerce

# Verify installation
oscommerce --version
```

### FreeBSD

```bash
# Using pkg
pkg install oscommerce

# Enable in rc.conf
echo 'oscommerce_enable="YES"' >> /etc/rc.conf

# Start service
service oscommerce start

# Verify installation
oscommerce --version
```

### Windows

```bash
# Using Chocolatey
choco install oscommerce

# Or using Scoop
scoop install oscommerce

# Verify installation
oscommerce --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/oscommerce

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
oscommerce --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable oscommerce

# Start service
sudo systemctl start oscommerce

# Stop service
sudo systemctl stop oscommerce

# Restart service
sudo systemctl restart oscommerce

# Check status
sudo systemctl status oscommerce

# View logs
sudo journalctl -u oscommerce -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add oscommerce default

# Start service
rc-service oscommerce start

# Stop service
rc-service oscommerce stop

# Restart service
rc-service oscommerce restart

# Check status
rc-service oscommerce status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'oscommerce_enable="YES"' >> /etc/rc.conf

# Start service
service oscommerce start

# Stop service
service oscommerce stop

# Restart service
service oscommerce restart

# Check status
service oscommerce status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start oscommerce
brew services stop oscommerce
brew services restart oscommerce

# Check status
brew services list | grep oscommerce
```

### Windows Service Manager

```powershell
# Start service
net start oscommerce

# Stop service
net stop oscommerce

# Using PowerShell
Start-Service oscommerce
Stop-Service oscommerce
Restart-Service oscommerce

# Check status
Get-Service oscommerce
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream oscommerce_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name oscommerce.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name oscommerce.example.com;

    ssl_certificate /etc/ssl/certs/oscommerce.example.com.crt;
    ssl_certificate_key /etc/ssl/private/oscommerce.example.com.key;

    location / {
        proxy_pass http://oscommerce_backend;
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
    ServerName oscommerce.example.com
    Redirect permanent / https://oscommerce.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName oscommerce.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/oscommerce.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/oscommerce.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend oscommerce_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/oscommerce.pem
    redirect scheme https if !{ ssl_fc }
    default_backend oscommerce_backend

backend oscommerce_backend
    balance roundrobin
    server oscommerce1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R oscommerce:oscommerce /etc/oscommerce
sudo chmod 750 /etc/oscommerce

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status oscommerce

# View logs
sudo journalctl -u oscommerce -f

# Monitor resource usage
top -p $(pgrep oscommerce)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/oscommerce"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/oscommerce-backup-$DATE.tar.gz" /etc/oscommerce /var/lib/oscommerce

echo "Backup completed: $BACKUP_DIR/oscommerce-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop oscommerce

# Restore from backup
tar -xzf /backup/oscommerce/oscommerce-backup-*.tar.gz -C /

# Start service
sudo systemctl start oscommerce
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u oscommerce -n 100
sudo tail -f /var/log/oscommerce/oscommerce.log

# Check configuration
oscommerce --version

# Check permissions
ls -la /etc/oscommerce
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep oscommerce)

# Check disk I/O
iotop -p $(pgrep oscommerce)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  oscommerce:
    image: oscommerce:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/oscommerce
      - ./data:/var/lib/oscommerce
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update oscommerce

# Debian/Ubuntu
sudo apt update && sudo apt upgrade oscommerce

# Arch Linux
sudo pacman -Syu oscommerce

# Alpine Linux
apk update && apk upgrade oscommerce

# openSUSE
sudo zypper update oscommerce

# FreeBSD
pkg update && pkg upgrade oscommerce

# Always backup before updates
tar -czf /backup/oscommerce-pre-update-$(date +%Y%m%d).tar.gz /etc/oscommerce

# Restart after updates
sudo systemctl restart oscommerce
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/oscommerce

# Clean old logs
find /var/log/oscommerce -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/oscommerce
```

## Additional Resources

- Official Documentation: https://docs.oscommerce.org/
- GitHub Repository: https://github.com/oscommerce/oscommerce
- Community Forum: https://forum.oscommerce.org/
- Best Practices Guide: https://docs.oscommerce.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
