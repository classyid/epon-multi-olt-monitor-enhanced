# 🚀 Panduan Deployment Enhanced EPON Multi-OLT Monitor

## 📋 Daftar Isi
1. [Persiapan Environment](#persiapan-environment)
2. [Deployment ke Server Linux](#deployment-ke-server-linux)
3. [Deployment dengan Docker](#deployment-dengan-docker)
4. [Deployment ke VPS/Cloud](#deployment-ke-vps-cloud)
5. [Setup Production dengan Nginx](#setup-production-dengan-nginx)
6. [Monitoring & Maintenance](#monitoring--maintenance)

## 🔧 Persiapan Environment

### System Requirements
- **OS**: Linux (Ubuntu 20.04+ / CentOS 8+ / Debian 10+)
- **Python**: 3.7 atau lebih baru
- **RAM**: Minimum 512MB (Rekomendasi 1GB+)
- **Storage**: Minimum 1GB free space
- **Network**: Akses ke OLT yang akan dimonitor

### Dependencies Required
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Python dan tools
sudo apt install -y python3 python3-pip python3-venv git curl

# Install build tools (untuk beberapa Python packages)
sudo apt install -y build-essential python3-dev
```

## 🖥️ Deployment ke Server Linux

### Method 1: Direct Installation

#### 1. Clone Repository
```bash
# Clone dari GitHub
git clone https://github.com/classyid/epon-multi-olt-monitor-enhanced.git
cd epon-multi-olt-monitor-enhanced

# Atau upload file langsung jika tidak menggunakan Git
# scp -r ./epon-multi-olt-monitor-enhanced user@server:/home/user/
```

#### 2. Setup Virtual Environment
```bash
# Buat virtual environment
python3 -m venv venv

# Aktivasi virtual environment
source venv/bin/activate

# Upgrade pip
pip install --upgrade pip
```

#### 3. Install Dependencies
```bash
# Buat file requirements.txt
cat > requirements.txt << EOF
Flask==2.3.3
Flask-SocketIO==5.3.6
requests==2.31.0
beautifulsoup4==4.12.2
python-socketio==5.8.0
python-engineio==4.7.1
EOF

# Install dependencies
pip install -r requirements.txt
```

#### 4. Konfigurasi Aplikasi
```bash
# Buat direktori untuk database dan logs
mkdir -p data logs

# Set permissions
chmod 755 data logs

# Copy script utama
cp epon_monitor_enhanced.py app.py
```

#### 5. Test Aplikasi
```bash
# Test run aplikasi
python app.py

# Jika berhasil, akses http://server-ip:5000
# Tekan Ctrl+C untuk stop
```

### Method 2: Systemd Service (Production)

#### 1. Buat Service File
```bash
sudo nano /etc/systemd/system/epon-monitor.service
```

#### 2. Isi Service Configuration
```ini
[Unit]
Description=Enhanced EPON Multi-OLT Monitor
After=network.target

[Service]
Type=simple
User=your-username
WorkingDirectory=/home/your-username/epon-multi-olt-monitor-enhanced
Environment=PATH=/home/your-username/epon-multi-olt-monitor-enhanced/venv/bin
ExecStart=/home/your-username/epon-multi-olt-monitor-enhanced/venv/bin/python app.py
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

#### 3. Enable dan Start Service
```bash
# Reload systemd
sudo systemctl daemon-reload

# Enable service
sudo systemctl enable epon-monitor

# Start service
sudo systemctl start epon-monitor

# Check status
sudo systemctl status epon-monitor

# View logs
sudo journalctl -u epon-monitor -f
```

## 🐳 Deployment dengan Docker

### 1. Buat Dockerfile
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Create data directory
RUN mkdir -p data logs

# Expose port
EXPOSE 5000

# Set environment variables
ENV FLASK_ENV=production
ENV PYTHONUNBUFFERED=1

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:5000/api/monitoring/status || exit 1

# Run application
CMD ["python", "epon_monitor_enhanced.py"]
```

### 2. Buat docker-compose.yml
```yaml
version: '3.8'

services:
  epon-monitor:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - ./data:/app/data
      - ./logs:/app/logs
    environment:
      - FLASK_ENV=production
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/api/monitoring/status"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - epon-monitor
    restart: unless-stopped
```

### 3. Build dan Run
```bash
# Build image
docker-compose build

# Run container
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f epon-monitor
```

## ☁️ Deployment ke VPS/Cloud

### AWS EC2 Deployment

#### 1. Launch EC2 Instance
```bash
# Pilih Ubuntu 20.04 LTS
# Instance type: t2.micro (free tier) atau t2.small
# Security Group: Allow HTTP (80), HTTPS (443), Custom (5000)
```

#### 2. Connect dan Setup
```bash
# Connect via SSH
ssh -i your-key.pem ubuntu@your-ec2-ip

# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Logout dan login ulang untuk docker group
exit
ssh -i your-key.pem ubuntu@your-ec2-ip
```

#### 3. Deploy Application
```bash
# Clone atau upload aplikasi
git clone https://github.com/username/epon-multi-olt-monitor-enhanced.git
cd epon-multi-olt-monitor-enhanced

# Deploy dengan Docker
docker-compose up -d

# Check status
docker-compose ps
```

### DigitalOcean Droplet

#### 1. Create Droplet
```bash
# Pilih Ubuntu 20.04
# Size: 1GB RAM minimum
# Add SSH key
```

#### 2. Setup sama seperti AWS EC2

### Google Cloud Platform

#### 1. Create VM Instance
```bash
# Compute Engine → VM instances
# Machine type: e2-micro atau e2-small
# Boot disk: Ubuntu 20.04 LTS
# Firewall: Allow HTTP dan HTTPS traffic
```

#### 2. Setup aplikasi sama seperti method sebelumnya

## 🌐 Setup Production dengan Nginx

### 1. Install Nginx
```bash
sudo apt install nginx -y
```

### 2. Konfigurasi Nginx
```bash
sudo nano /etc/nginx/sites-available/epon-monitor
```

### 3. Nginx Configuration File
```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com www.your-domain.com;

    # SSL Configuration
    ssl_certificate /etc/ssl/certs/your-domain.crt;
    ssl_certificate_key /etc/ssl/private/your-domain.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384;
    ssl_prefer_server_ciphers off;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # Static files (jika ada)
    location /static {
        alias /home/user/epon-multi-olt-monitor-enhanced/static;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Security
    location ~ /\. {
        deny all;
    }
}
```

### 4. Enable Site
```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/epon-monitor /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Restart nginx
sudo systemctl restart nginx

# Enable nginx autostart
sudo systemctl enable nginx
```

### 5. SSL Certificate dengan Let's Encrypt
```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx -y

# Get certificate
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

# Auto renewal
sudo systemctl enable certbot.timer
```

## 📊 Monitoring & Maintenance

### 1. Log Monitoring
```bash
# System logs
sudo journalctl -u epon-monitor -f

# Application logs
tail -f logs/app.log

# Nginx logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# Docker logs
docker-compose logs -f epon-monitor
```

### 2. Health Checks
```bash
# Check application status
curl http://localhost:5000/api/monitoring/status

# Check system resources
htop
df -h
free -m

# Check service status
sudo systemctl status epon-monitor nginx
```

### 3. Backup Strategy
```bash
# Database backup script
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/user/backups"
mkdir -p $BACKUP_DIR

# Backup database
cp /home/user/epon-multi-olt-monitor-enhanced/data/epon_monitor.db $BACKUP_DIR/epon_monitor_$DATE.db

# Backup configuration
tar -czf $BACKUP_DIR/config_$DATE.tar.gz /home/user/epon-multi-olt-monitor-enhanced/

# Keep only last 7 days
find $BACKUP_DIR -name "*.db" -type f -mtime +7 -delete
find $BACKUP_DIR -name "*.tar.gz" -type f -mtime +7 -delete

echo "Backup completed: $DATE"
```

### 4. Update Procedure
```bash
# Stop service
sudo systemctl stop epon-monitor

# Backup current version
cp -r /home/user/epon-multi-olt-monitor-enhanced /home/user/epon-backup-$(date +%Y%m%d)

# Pull updates
cd /home/user/epon-multi-olt-monitor-enhanced
git pull origin main

# Update dependencies
source venv/bin/activate
pip install -r requirements.txt

# Start service
sudo systemctl start epon-monitor

# Check status
sudo systemctl status epon-monitor
```

### 5. Performance Optimization
```bash
# Optimize database
sqlite3 data/epon_monitor.db "VACUUM;"

# Clear old logs
find logs/ -name "*.log" -type f -mtime +30 -delete

# Monitor system resources
sudo apt install htop iotop nethogs -y
```

## 🔧 Troubleshooting

### Common Issues

#### Port Already in Use
```bash
# Check what's using port 5000
sudo lsof -i :5000
sudo netstat -tulnp | grep :5000

# Kill process if needed
sudo kill -9 PID
```

#### Permission Issues
```bash
# Fix file permissions
chmod +x epon_monitor_enhanced.py
chown -R user:user /home/user/epon-multi-olt-monitor-enhanced
```

#### Memory Issues
```bash
# Check memory usage
free -m
ps aux --sort=-%mem | head

# Restart service if needed
sudo systemctl restart epon-monitor
```

#### Database Lock
```bash
# Check database integrity
sqlite3 data/epon_monitor.db ".timeout 30000"
sqlite3 data/epon_monitor.db "PRAGMA integrity_check;"
```

## 📈 Performance Tuning

### 1. Application Settings
- Sesuaikan interval monitoring berdasarkan jumlah OLT
- Gunakan concurrent processing untuk multi-OLT
- Implementasi connection pooling

### 2. System Settings
```bash
# Increase file descriptors
echo "* soft nofile 65536" >> /etc/security/limits.conf
echo "* hard nofile 65536" >> /etc/security/limits.conf

# Network optimization
echo 'net.core.somaxconn = 1024' >> /etc/sysctl.conf
sysctl -p
```

### 3. Database Optimization
```sql
-- Create indexes for better performance
CREATE INDEX IF NOT EXISTS idx_onu_status_olt_id ON onu_status(olt_id);
CREATE INDEX IF NOT EXISTS idx_alert_history_created_at ON alert_history(created_at);
CREATE INDEX IF NOT EXISTS idx_monitoring_stats_timestamp ON monitoring_stats(timestamp);
```

---

**✅ Deployment selesai! Aplikasi Enhanced EPON Multi-OLT Monitor siap digunakan.**
