# Private Configuration Template

> ⚠️ **DO NOT COMMIT THIS FILE WITH REAL DATA**
> 
> This template is for your personal use to document your actual infrastructure.
> Save your version as `PRIVATE_CONFIG.md` or similar (already in .gitignore)

---

## ⚠️ Security Notice

**This file should contain your ACTUAL infrastructure details and must NEVER be committed to a public repository.**

**Store this file:**
- In a password manager (recommended)
- In a private Git repository
- On an encrypted drive
- In a secure note-taking app

---

## Network Configuration

### IP Addressing

| Device | IP Address | MAC Address | Purpose |
|--------|------------|-------------|---------|
| Proxmox Host | | | Hypervisor |
| Plex VM | | | Media server |
| Router/Gateway | | | Network gateway |
| Additional VMs | | | |

**Subnet:** _________________  
**Subnet Mask:** _________________  
**DNS Servers:** _________________  
**DHCP Range:** _________________ (avoid this range for static IPs)

---

## Proxmox Configuration

**Management URL:** https://___________________:8006  
**Username:** _________________  
**SSH Port:** _________ (if non-standard)

### VM IDs

| VMID | VM Name | Purpose | IP Address |
|------|---------|---------|------------|
| | | | |
| | | | |
| | | | |

---

## Storage Configuration

### ZFS Pools

**Pool 1 Name:** _________________  
- **Capacity:** _________________  
- **Current Usage:** _________________  
- **Drive Configuration:** _________________  
- **Mount Point:** _________________

**Pool 2 Name:** _________________  
- **Capacity:** _________________  
- **Current Usage:** _________________  
- **Drive Configuration:** _________________  
- **Mount Point:** _________________

---

## Plex Configuration

**Plex Server IP:** _________________  
**Plex URL:** https://_________________:32400/web  
**Admin Email:** _________________  

### Library Paths

- Movies: _________________
- TV Shows: _________________
- Music: _________________
- Photos: _________________

### Remote Access

**Domain:** _________________ (if using custom domain)  
**Cloudflare Tunnel:** _________________ (if applicable)  
**Port Forwarding:** _________ → _________ (if applicable)

---

## Credentials

> 🔒 Store passwords in a password manager, NOT here

**Proxmox Root:**
- Username: root
- Password: [Use password manager]

**Plex Admin:**
- Email: _________________
- Password: [Use password manager]

**SSH Keys:**
- Location: _________________
- Backup location: _________________

---

## Cloudflare Configuration

**Account Email:** _________________  
**Tunnel Name:** _________________ (if using)  
**API Token:** [Use password manager or env var]

---

## Backup Configuration

### Local Backups

**ZFS Snapshot Schedule:**
- Hourly: _________________ (retention)
- Daily: _________________ (retention)
- Weekly: _________________ (retention)
- Monthly: _________________ (retention)

### Off-Site Backups

**Destination:** _________________  
**Frequency:** _________________  
**Last Backup:** _________________  
**Backup Size:** _________________

---

## Hardware Details

**Server Model:** Dell PowerEdge R730 (or your actual model)  
**Serial Number:** _________________  
**iDRAC IP:** _________________ (if applicable)  
**iDRAC Credentials:** [Use password manager]

### Drives

| Drive | Size | Model | Serial | Purpose | SMART Status |
|-------|------|-------|--------|---------|--------------|
| | | | | | |
| | | | | | |

---

## Network Hardware

**Switch Model:** _________________  
**Switch IP:** _________________  
**Switch Credentials:** [Use password manager]

**Router Model:** _________________  
**Router IP:** _________________  
**Router Credentials:** [Use password manager]

---

## Monitoring & Alerts

**Monitoring Tool:** _________________  
**Alert Email:** _________________  
**Alert Phone:** _________________ (if SMS alerts)

---

## ISP Information

**Provider:** _________________  
**Account Number:** _________________  
**Download Speed:** _________________  
**Upload Speed:** _________________  
**Static IP:** _________________ (if applicable)

---

## Support Contacts

**Hardware Vendor:** _________________  
**Support Phone:** _________________  
**Support Portal:** _________________

---

## Important Notes

**Installation Date:** _________________  
**Last Major Update:** _________________  
**Next Maintenance:** _________________

### Custom Configurations

Document any custom settings, scripts, or non-standard configurations here:

---

## Emergency Recovery

**Recovery USB Location:** _________________  
**Config Backup Location:** _________________  
**Emergency Contact:** _________________

---

**Last Updated:** _________________  
**Reviewed By:** _________________





