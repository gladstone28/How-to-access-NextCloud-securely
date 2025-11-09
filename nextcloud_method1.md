# Secure External Access to Nextcloud (Method 1 — Cloudflare Tunnel)

## Overview
This guide explains how to securely access Nextcloud from outside your home network using Cloudflare Tunnel. No port forwarding is required.

## Requirements
- Cloudflare account (free)
- A domain name
- Raspberry Pi with Nextcloud installed

## Steps

### 1. Create Cloudflare Account
Go to https://dash.cloudflare.com and register.

### 2. Add Domain to Cloudflare
Update your domain’s nameservers to Cloudflare's.

### 3. Install Cloudflare Tunnel
```
curl -fsSL https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb -o cloudflared.deb
sudo dpkg -i cloudflared.deb
sudo cloudflared service install
```

### 4. Authenticate
```
cloudflared tunnel login
```

### 5. Create Tunnel
```
cloudflared tunnel create nextcloud-pi
```

### 6. Route Public Hostname
Example:
```
cloudflared tunnel route dns nextcloud-pi cloud.mydomain.com
```

### 7. Configure Tunnel
Edit `/etc/cloudflared/config.yml`:
```
tunnel: nextcloud-pi
credentials-file: /root/.cloudflared/<TUNNEL-ID>.json

ingress:
  - hostname: cloud.mydomain.com
    service: http://127.0.0.1:80
  - service: http_status:404
```

### 8. Start Tunnel
```
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

### 9. Add Trusted Domain
Edit `config.php`:
```
2 => 'cloud.mydomain.com',
```

Nextcloud is now securely accessible anywhere.
