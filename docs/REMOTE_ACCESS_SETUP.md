# Remote Access Setup - Cloudflare Tunnel & Reverse Proxy

**Document Type:** Technical Configuration Guide  
**Last Updated:** November 6, 2025  
**Target Audience:** System Administrators, DevOps Engineers  
**Prerequisites:** Proxmox VE, Cloudflare account, Basic networking knowledge

---

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Prerequisites](#prerequisites)
4. [Cloudflare DNS Configuration](#cloudflare-dns-configuration)
5. [Reverse Proxy Setup (VM120)](#reverse-proxy-setup-vm120)
6. [Plex Network Configuration](#plex-network-configuration)
7. [Testing & Validation](#testing--validation)
8. [Troubleshooting](#troubleshooting)
9. [Security Considerations](#security-considerations)

---

## Overview

This guide documents the production configuration for providing secure, high-performance remote access to SethFlix Plex Media Server using Cloudflare Tunnel and Nginx reverse proxy.

### Why This Approach?

**Benefits over traditional port forwarding:**
- ✅ No exposed public ports (increased security)
- ✅ DDoS protection via Cloudflare
- ✅ SSL/TLS termination at Cloudflare edge
- ✅ Geographic content delivery optimization
- ✅ No dynamic DNS requirements
- ✅ Advanced traffic filtering and analytics

### Architecture Components

1. **Cloudflare Tunnel** (`cloudflared`) - Secure tunnel from VM120 to Cloudflare edge
2. **Nginx Reverse Proxy** (VM120) - Local HTTP routing and header management
3. **Plex Media Server** (VM200) - Media streaming backend
4. **Cloudflare DNS** - Domain management and routing

---

## Architecture

### Network Flow

```
External User
    ↓
Cloudflare Edge (SSL/TLS)
    ↓
Cloudflare Tunnel (encrypted)
    ↓
VM120 (Reverse Proxy Gateway)
  ├─ Nginx :80
  └─ cloudflared service
       ↓
VM200 (Plex Media Server)
  └─ Plex :32400
```

### VM Specifications

**VM120 - Reverse Proxy Gateway**
- **Purpose:** Nginx reverse proxy, Cloudflare tunnel endpoint
- **OS:** Ubuntu 22.04/24.04 LTS
- **IP:** Configure static IP on your network
- **Network Bridge:** vmbr2 (adjust per your Proxmox config)
- **Services:** nginx, cloudflared

**VM200 - Plex Media Server**
- **Purpose:** SethFlix streaming backend
- **OS:** Windows Server 2025 Datacenter
- **IP:** Configure static IP on your network
- **Storage:** Network-mounted ZFS storage (P: drive)
- **Services:** Plex Media Server

---

## Prerequisites

### Required Accounts & Services

1. **Cloudflare Account**
   - Domain added to Cloudflare
   - API Token with DNS edit permissions
   - Tunnel created (see Cloudflare documentation)

2. **Domain Name**
   - Nameservers pointed to Cloudflare
   - DNS zone active on Cloudflare

3. **Proxmox Environment**
   - VM120: Ubuntu Linux VM with nginx
   - VM200: Windows Server VM with Plex installed
   - Network connectivity between VMs

### Software Versions

- **Proxmox VE:** 7.0+ or 8.0+
- **Ubuntu:** 22.04 LTS or 24.04 LTS (VM120)
- **Windows Server:** 2019, 2022, or 2025 (VM200)
- **Nginx:** 1.24.0+
- **cloudflared:** Latest stable
- **Plex Media Server:** 1.40.0+

---

## Cloudflare DNS Configuration

### Step 1: Create Cloudflare Tunnel

1. Log into Cloudflare Dashboard: https://dash.cloudflare.com
2. Select your domain (e.g., `lightspeedup.com`)
3. Navigate to **Zero Trust → Access → Tunnels**
4. Click **Create a tunnel**
5. Name it (e.g., `norelec-tunnel`)
6. Install `cloudflared` on VM120 following Cloudflare's instructions
7. Note your tunnel UUID (format: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`)

### Step 2: Configure DNS Records

Navigate to **DNS → Records** in Cloudflare Dashboard.

#### Required CNAME Records

| Type | Name | Target | Proxy Status | TTL |
|------|------|--------|--------------|-----|
| CNAME | plex | `YOUR-TUNNEL-UUID.cfargotunnel.com` | Proxied (🟠) | Auto |
| CNAME | @ (root) | `YOUR-TUNNEL-UUID.cfargotunnel.com` | Proxied (🟠) | Auto |
| CNAME | www | `YOUR-TUNNEL-UUID.cfargotunnel.com` | Proxied (🟠) | Auto |

**Example with real tunnel UUID:**
```
plex.lightspeedup.com → 624c59c6-b364-4488-85e5-90225351b0e2.cfargotunnel.com
```

> ⚠️ **IMPORTANT:** Always use **Proxied (🟠 orange cloud)** status for security and performance.

---

## Reverse Proxy Setup (VM120)

### Step 1: Install Required Packages

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Nginx
sudo apt install nginx -y

# Install cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb
```

### Step 2: Configure Cloudflare Tunnel

Create tunnel configuration file:

```bash
sudo mkdir -p /etc/cloudflared
sudo nano /etc/cloudflared/config.yml
```

**Configuration:**

```yaml
tunnel: YOUR-TUNNEL-NAME
credentials-file: /home/YOUR-USER/.cloudflared/YOUR-TUNNEL-UUID.json

ingress:
  # Plex Media Server
  - hostname: plex.yourdomain.com
    service: http://localhost:80
  
  # Main domain
  - hostname: yourdomain.com
    service: http://WORDPRESS-VM-IP:80
  
  # www subdomain
  - hostname: www.yourdomain.com
    service: http://WORDPRESS-VM-IP:80
  
  # Catch-all rule (required)
  - service: http_status:404
```

**Real-world example:**

```yaml
tunnel: norelec-tunnel
credentials-file: /home/proxy1/.cloudflared/624c59c6-b364-4488-85e5-90225351b0e2.json

ingress:
  - hostname: plex.lightspeedup.com
    service: http://localhost:80
  - hostname: lightspeedup.com
    service: http://192.168.12.150:80
  - hostname: www.lightspeedup.com
    service: http://192.168.12.150:80
  - service: http_status:404
```

### Step 3: Install cloudflared as System Service

```bash
# Install service
sudo cloudflared service install

# Enable service
sudo systemctl enable cloudflared

# Start service
sudo systemctl start cloudflared

# Verify status
sudo systemctl status cloudflared
```

### Step 4: Configure Nginx for Plex

Create Nginx site configuration:

```bash
sudo nano /etc/nginx/sites-available/plex.yourdomain.com.conf
```

**Nginx Configuration:**

```nginx
server {
    listen 80;
    server_name plex.yourdomain.com;

    # Plex-specific timeout settings
    send_timeout 100m;
    proxy_read_timeout 100m;
    proxy_connect_timeout 100m;

    # Plex headers
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Sec-WebSocket-Extensions $http_sec_websocket_extensions;
    proxy_set_header Sec-WebSocket-Key $http_sec_websocket_key;
    proxy_set_header Sec-WebSocket-Version $http_sec_websocket_version;

    # WebSocket support for Plex
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";

    location / {
        # Replace with your Plex VM IP
        proxy_pass http://PLEX-VM-IP:32400;
        proxy_buffering off;
    }
}
```

**Real-world example:**

```nginx
server {
    listen 80;
    server_name plex.lightspeedup.com;

    send_timeout 100m;
    proxy_read_timeout 100m;
    proxy_connect_timeout 100m;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Sec-WebSocket-Extensions $http_sec_websocket_extensions;
    proxy_set_header Sec-WebSocket-Key $http_sec_websocket_key;
    proxy_set_header Sec-WebSocket-Version $http_sec_websocket_version;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";

    location / {
        proxy_pass http://192.168.12.200:32400;
        proxy_buffering off;
    }
}
```

### Step 5: Enable Site and Restart Nginx

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/plex.yourdomain.com.conf /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx

# Verify status
sudo systemctl status nginx
```

---

## Plex Network Configuration

### Step 1: Access Plex Settings

1. RDP or access VM200 (Plex server)
2. Open web browser
3. Navigate to: `http://localhost:32400/web`
4. Log in to Plex
5. Click **Settings** (⚙️ icon) → **Network**

### Step 2: Configure Custom Server Access URL

**Setting:** Custom server access URLs

**Value:**
```
https://plex.yourdomain.com
```

**Example:**
```
https://plex.lightspeedup.com
```

> ⚠️ **CRITICAL:** Use `https://` (not `http://`). Cloudflare terminates SSL.

### Step 3: Allow Cloudflare IP Ranges

**Setting:** List of IP addresses and networks that are allowed without auth

**Cloudflare IP Ranges to Add:**

```
173.245.48.0/20
103.21.244.0/22
103.22.200.0/22
103.31.4.0/22
141.101.64.0/18
108.162.192.0/18
190.93.240.0/20
188.114.96.0/20
197.234.240.0/22
198.41.128.0/17
162.158.0.0/15
104.16.0.0/13
104.24.0.0/14
172.64.0.0/13
131.0.72.0/22
```

**How to add:**
1. Copy all IP ranges above
2. Paste into "List of IP addresses and networks that are allowed without auth" field
3. Separate ranges with commas or spaces

### Step 4: Configure Secure Connections

**Setting:** Secure connections

**Value:** **Preferred** (not "Required")

> ℹ️ "Preferred" allows local LAN connections while enforcing HTTPS for external access.

### Step 5: Disable Built-in Remote Access

**Setting:** Enable Remote Access

**Action:** 
1. **Uncheck** "Manually specify public port"
2. Leave "Enable Remote Access" disabled/unchecked

> ✅ This is expected and correct! You're using Cloudflare tunnel instead.

### Step 6: Configure Internet Upload Speed (Optional)

**Setting:** Internet upload speed

**Value:** Enter your actual upload speed in Mbps

Test at: https://speedtest.net

**Example:** If you have 30 Mbps upload, enter `30`

### Step 7: Save All Settings

Click **Save Changes** at bottom of settings page.

---

## Testing & Validation

### Test 1: Local Plex Access

On Plex VM (VM200):

```powershell
# Test local Plex accessibility
Invoke-WebRequest -Uri http://127.0.0.1:32400 -Method GET -UseBasicParsing
```

**Expected:** HTTP 200 or 302 redirect

### Test 2: Reverse Proxy Connectivity

On VM120:

```bash
# Test Nginx to Plex connection
curl -I http://localhost:80
```

**Expected:** HTTP 302 redirect with Plex headers

### Test 3: Cloudflare Tunnel Status

On VM120:

```bash
# Check cloudflared service
sudo systemctl status cloudflared

# View tunnel logs
sudo journalctl -u cloudflared -f
```

**Expected:** Service active and running, no error messages

### Test 4: External HTTPS Access

From any external device (not on your network):

```bash
# Test HTTPS access
curl -I https://plex.yourdomain.com
```

**Or open in browser:**
```
https://plex.yourdomain.com/web
```

**Expected:** 
- HTTP 200 or 302
- Plex web interface loads
- SSL certificate valid (issued by Cloudflare)

### Test 5: Plex Web Interface

Open in browser:

```
https://plex.yourdomain.com/web
```

**Expected:**
- Plex login page or dashboard
- No SSL warnings
- Server "SethFlix-2025" appears

### Test 6: Remote Streaming

From external network:

1. Open Plex app or web interface
2. Log in with Plex account
3. Select your server
4. Play a video

**Expected:**
- Server appears in server list
- Videos play without buffering (depending on upload speed)
- Transcoding works if needed

---

## Troubleshooting

### Issue: "Not available outside your network" in Plex Settings

**Status:** ✅ **This is NORMAL and EXPECTED**

**Explanation:**
- This warning appears because Plex's built-in Remote Access is disabled
- You're using Cloudflare tunnel instead (the professional method)
- External users connect via your custom URL, not Plex's relay

**Verification:**
- Can you access `https://plex.yourdomain.com/web` externally? ✅ **Working**
- Do remote users see your server in their apps? ✅ **Working**

### Issue: HTTP 530 Error from Cloudflare

**Symptoms:** "Error 530: Origin DNS error"

**Causes:**
1. DNS record pointing to wrong tunnel
2. cloudflared service not running
3. Nginx not configured correctly

**Solutions:**

```bash
# Check DNS record target
# Should be: YOUR-TUNNEL-UUID.cfargotunnel.com

# Verify cloudflared is running
sudo systemctl status cloudflared

# Restart cloudflared
sudo systemctl restart cloudflared

# Check Nginx configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Issue: HTTP 401 Unauthorized

**Symptoms:** Plex returns 401 errors to Cloudflare

**Cause:** Cloudflare IP ranges not whitelisted in Plex

**Solution:**
1. Go to Plex Settings → Network
2. Add all Cloudflare IP ranges to "List of IP addresses and networks that are allowed without auth"
3. Save changes
4. Wait 30 seconds
5. Test again

### Issue: Videos Won't Play Remotely

**Symptoms:** Server appears but videos won't stream

**Possible Causes:**
1. Insufficient upload bandwidth
2. Transcoding not configured
3. Library permissions not set

**Solutions:**

```powershell
# On VM200, check if Plex is running
Get-Process | Where-Object {$_.ProcessName -like "*Plex*"}

# Expected: Plex Media Server process running
```

**In Plex Settings:**
1. **Settings → Network → Internet upload speed**: Set correctly
2. **Settings → Transcoder**: Enable hardware transcoding if available
3. **Settings → Users & Sharing**: Verify user has library access

### Issue: Cloudflared Service Won't Start

**Symptoms:** `sudo systemctl status cloudflared` shows "failed"

**Solutions:**

```bash
# Check configuration syntax
sudo cloudflared tunnel ingress validate

# View detailed logs
sudo journalctl -u cloudflared -n 50 --no-pager

# Verify credentials file exists
ls -la /home/YOUR-USER/.cloudflared/

# Expected: *.json credentials file present

# Restart service
sudo systemctl restart cloudflared
```

### Issue: Nginx Configuration Test Fails

**Symptoms:** `sudo nginx -t` shows errors

**Solutions:**

```bash
# View detailed error
sudo nginx -t

# Check configuration file syntax
sudo nano /etc/nginx/sites-enabled/plex.yourdomain.com.conf

# Common issues:
# - Missing semicolons
# - Incorrect proxy_pass IP
# - Duplicate server_name entries

# Remove site temporarily
sudo rm /etc/nginx/sites-enabled/plex.yourdomain.com.conf

# Test again
sudo nginx -t

# Re-enable once fixed
sudo ln -s /etc/nginx/sites-available/plex.yourdomain.com.conf /etc/nginx/sites-enabled/
```

---

## Security Considerations

### Cloudflare Protection Features

**Enabled by default:**
- ✅ DDoS protection
- ✅ SSL/TLS encryption
- ✅ Bot protection
- ✅ Rate limiting

**Recommended enhancements:**

1. **Enable Cloudflare WAF (Web Application Firewall)**
   - Navigate to: Security → WAF
   - Enable managed rules
   - Configure challenge for suspicious traffic

2. **Configure Rate Limiting**
   - Navigate to: Security → Rate Limiting Rules
   - Create rule: Max 100 requests per minute per IP
   - Action: Challenge or block

3. **Enable Security Headers**
   - Navigate to: Security → Settings
   - Enable: HSTS, CSP, X-Frame-Options

### Nginx Security Headers

Add to Nginx configuration:

```nginx
server {
    # ... existing configuration ...

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # ... rest of configuration ...
}
```

### Plex Security Best Practices

1. **Strong Admin Password**
   - Use 16+ character password
   - Enable two-factor authentication (2FA) on Plex account

2. **User Permissions**
   - Only share necessary libraries
   - Review shared users regularly
   - Revoke access for inactive users

3. **Regular Updates**
   - Keep Plex Media Server updated
   - Apply Windows security patches
   - Update cloudflared and Nginx regularly

4. **Monitoring**
   - Enable Plex logging
   - Monitor cloudflared logs for anomalies
   - Review Cloudflare Analytics regularly

### Network Isolation

**Recommended firewall rules on VM200 (Plex):**

```powershell
# Allow only from reverse proxy VM
New-NetFirewallRule -DisplayName "Plex from Reverse Proxy" -Direction Inbound -LocalPort 32400 -Protocol TCP -RemoteAddress YOUR-VM120-IP -Action Allow

# Block all other external access to port 32400
New-NetFirewallRule -DisplayName "Block Plex External" -Direction Inbound -LocalPort 32400 -Protocol TCP -Action Block
```

**Example:**

```powershell
New-NetFirewallRule -DisplayName "Plex from VM120" -Direction Inbound -LocalPort 32400 -Protocol TCP -RemoteAddress 192.168.12.120 -Action Allow
New-NetFirewallRule -DisplayName "Block Plex External" -Direction Inbound -LocalPort 32400 -Protocol TCP -Action Block
```

---

## Maintenance

### Regular Tasks

**Weekly:**
- Check Cloudflare Analytics for unusual traffic
- Review Plex server activity logs
- Verify all services running: `sudo systemctl status nginx cloudflared`

**Monthly:**
- Update cloudflared: `sudo apt update && sudo apt upgrade cloudflared`
- Update Nginx: `sudo apt update && sudo apt upgrade nginx`
- Update Plex Media Server (via Plex web interface)
- Review and rotate Cloudflare API tokens if compromised

**Quarterly:**
- Review Cloudflare IP ranges (they may update)
- Test disaster recovery procedures
- Review user access list in Plex

### Monitoring Commands

```bash
# Check cloudflared tunnel status
sudo cloudflared tunnel info YOUR-TUNNEL-NAME

# View Nginx access logs
sudo tail -f /var/log/nginx/access.log

# View Nginx error logs
sudo tail -f /var/log/nginx/error.log

# Check cloudflared metrics
sudo journalctl -u cloudflared --since "1 hour ago"
```

---

## Performance Optimization

### Nginx Tuning

Add to `/etc/nginx/nginx.conf` in `http` block:

```nginx
http {
    # ... existing config ...

    # Optimize for Plex streaming
    client_max_body_size 0;
    proxy_buffering off;
    proxy_request_buffering off;
    
    # Connection settings
    keepalive_timeout 65;
    keepalive_requests 100;
    
    # Proxy settings
    proxy_connect_timeout 600s;
    proxy_send_timeout 600s;
    proxy_read_timeout 600s;
}
```

### Cloudflare Settings

1. **Enable Argo Smart Routing** (paid feature)
   - Reduces latency by 30%+
   - Navigate to: Network → Argo Smart Routing

2. **Configure Caching Rules**
   - Navigate to: Caching → Configuration
   - Set cache level: Standard
   - Browser cache TTL: 4 hours

3. **Enable HTTP/3**
   - Navigate to: Network → HTTP/3
   - Toggle: Enabled

### Plex Transcoding Optimization

On VM200 (Windows Server):

1. **Settings → Transcoder**
   - Transcoder quality: Automatic
   - Enable hardware acceleration (if GPU available)
   - Transcoder temporary directory: Place on fast SSD

2. **Settings → Network**
   - LAN Networks: Add your local subnet (e.g., `192.168.1.0/24`)
   - Treat WAN IP as LAN: Enabled (if applicable)

---

## Additional Resources

### Official Documentation

- **Cloudflare Tunnels:** https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/
- **Nginx Documentation:** https://nginx.org/en/docs/
- **Plex Media Server:** https://support.plex.tv/articles/

### Community Resources

- **Plex Forums:** https://forums.plex.tv/
- **Cloudflare Community:** https://community.cloudflare.com/
- **r/Plex Subreddit:** https://reddit.com/r/plex

### Related Documentation

- [Getting Started Guide](GETTING_STARTED.md)
- [FAQ](FAQ.md)
- [Main README](../README.md)
- [Private Configuration Template](../PRIVATE_CONFIG_TEMPLATE.md)

---

## Changelog

**November 6, 2025:**
- Initial documentation created
- Documented production Cloudflare tunnel configuration
- Added Nginx reverse proxy setup
- Included Plex network settings
- Added troubleshooting guide
- Security best practices documented

---

## Support

For issues or questions:

1. **Check [FAQ](FAQ.md)** first
2. **Search existing [GitHub Issues](https://github.com/MatoTeziTanka/SethFlix-Plex/issues)**
3. **Create new issue** with detailed description
4. **Join community discussions** in GitHub Discussions

---

**Document maintained by:** SethFlix Infrastructure Team  
**License:** Apache 2.0  
**Repository:** https://github.com/MatoTeziTanka/SethFlix-Plex

