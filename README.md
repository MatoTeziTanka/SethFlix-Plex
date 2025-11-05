# SethFlix Infrastructure Documentation

**Project:** SethFlix Media Server  
**Repository:** https://github.com/MatoTeziTanka/SethFlix-Plex  
**License:** Apache-2.0  
**Last Updated:** November 4, 2025

---

## Table of Contents

1. [Overview](#overview)
2. [Hardware Infrastructure](#hardware-infrastructure)
3. [Network Architecture](#network-architecture)
4. [Storage Architecture](#storage-architecture)
5. [Virtual Machine Configuration](#virtual-machine-configuration)
6. [Plex Media Server Setup](#plex-media-server-setup)
7. [File Organization & Naming](#file-organization--naming)
8. [Backup Strategy](#backup-strategy)
9. [Performance Optimization](#performance-optimization)
10. [Future Roadmap](#future-roadmap)

---

## Overview

SethFlix is a self-hosted media streaming platform built on enterprise-grade hardware, leveraging Proxmox virtualization and ZFS storage for reliability and performance. This infrastructure serves as both a personal media server and a reference implementation for high-availability home lab setups.

### Key Features
- **Enterprise Hardware:** Dell PowerEdge R730 server
- **Virtualization:** Proxmox Virtual Environment (PVE)
- **Storage:** ZFS with redundancy and data integrity
- **Media Server:** Plex Media Server
- **Network:** 10GbE capable infrastructure
- **High Availability:** UPS-backed power, redundant storage

---

## Hardware Infrastructure

### Server Specifications

**Dell PowerEdge R730**
- **CPU:** Dual Intel Xeon E5-2698 v3 @ 2.30GHz (64 cores total)
- **RAM:** 480GB DDR4 ECC
- **Network:** 10GbE capable NICs
- **Power:** Dual redundant PSUs with UPS backup
- **Form Factor:** 2U rackmount

### Storage Configuration

**SSD Storage:**
- **Type:** Samsung PM883 Enterprise SATA SSDs
- **Capacity:** 4x 3.84TB drives in RAIDZ2 configuration
- **Purpose:** OS, databases, high-performance workloads
- **Pool Name:** `Storage_SSD` (14TB total capacity)

**HDD Storage:**
- **Type:** Enterprise-grade HDDs
- **Capacity:** 36.4TB total capacity
- **Configuration:** Multiple drives in RAIDZ configuration
- **Purpose:** Bulk media storage, backups
- **Pool Name:** `Storage_HDD`

**Premium SSD Pool:**
- **Capacity:** 668GB
- **Purpose:** Premium tier for performance-critical applications
- **Pool Name:** `Premium_SSD`

---

## Network Architecture

### IP Addressing Scheme

**Network Segment:** 192.168.12.0/24

| Host | IP Address | Purpose |
|------|------------|---------|
| Proxmox Host | 192.168.12.70 | Hypervisor management |
| WordPress VM | 192.168.12.150 | LightSpeedUp website |
| Management VM | 192.168.12.70:2222 | Infrastructure management (VM101) |
| Plex Media Server | TBD | SethFlix streaming |

### Network Features
- **Gateway:** 192.168.12.1
- **Bandwidth:** 10GbE capable
- **External Access:** Cloudflare for CDN and DDoS protection
- **VPN:** Planned for secure remote access

---

## Storage Architecture

### ZFS Pool Strategy

#### Storage_HDD (Plex Media Storage)
- **Total Capacity:** 36.4TB
- **Current Usage:** 23.9TB (65% full)
- **Free Space:** 12.5TB
- **Health:** ONLINE
- **Purpose:** Primary Plex media library storage
- **Features:**
  - Data integrity checksumming
  - Automatic corruption detection and repair
  - Snapshot support for backups
  - Compression enabled

#### Storage_SSD (High-Performance Storage)
- **Total Capacity:** 14.0TB
- **Current Usage:** 279GB (1% full)
- **Free Space:** 13.7TB
- **Health:** ONLINE
- **Purpose:** OS, databases, transcoding cache
- **Configuration:** RAIDZ2 (double parity)

### Dataset Structure

```
Storage_HDD/
├── SethFlix/                    # Main Plex media
│   ├── Movies/
│   ├── TV Shows/
│   ├── Music/
│   └── Photos/
└── Backups/                     # Backup datasets
```

### Storage Best Practices
1. **Snapshots:** Automated ZFS snapshots for point-in-time recovery
2. **Scrubbing:** Monthly data integrity checks
3. **Monitoring:** Real-time pool health monitoring
4. **Capacity Planning:** Maintain 20% free space for optimal performance

---

## Virtual Machine Configuration

### Proxmox Virtual Environment

**Version:** Proxmox VE 8.x  
**Kernel:** 6.8.12-15-pve  
**Management Interface:** https://192.168.12.70:8006

### Current VMs

| VMID | Name | Status | RAM | Storage | Purpose |
|------|------|--------|-----|---------|---------|
| 100 | Dev-AI-Server-2025 | Running | 32GB | Storage_SSD | Development |
| 101 | Management-AI-Assistant-1 | Running | 64GB | Storage_SSD | Infrastructure management |
| 120 | Reverse-Proxy-Gateway | Running | 6GB | Storage_SSD | Nginx reverse proxy |
| 150 | WordPress-Hosting-1 | Running | 32GB | Storage_SSD | LightSpeedUp.com |
| 160 | Database-Services-1 | Stopped | 32GB | Storage_SSD | MySQL/PostgreSQL |
| 170 | Game-Server-Hosting-1 | Stopped | 48GB | Storage_SSD | Game hosting |
| 180 | API-Services-1 | Stopped | 24GB | Storage_SSD | API backend |
| 192 | Personal | Stopped | 24GB | Storage_SSD | Personal use |
| 200 | **SethFlix-Media-Server-2025** | Stopped | 96GB | Storage_HDD | **Plex Media Server** |
| 201 | StreamForge-Development-2025 | Stopped | 64GB | Storage_SSD | Streaming development |
| 300 | win-2025 | Stopped | 64GB | Storage_SSD | Windows Server |

### SethFlix VM Specifications (VM200)

**Operating System:** Ubuntu 24.04 LTS (planned)  
**RAM:** 96GB  
**CPU:** 12-16 vCPUs (configurable based on load)  
**Storage:**
- **Boot Disk:** 100GB on Storage_SSD
- **Media Library:** Direct access to Storage_HDD/SethFlix
- **Transcoding Cache:** 200GB on Storage_SSD

**Network:**
- **IP:** TBD (192.168.12.x)
- **Bridge:** vmbr0 (bridged to physical network)
- **Bandwidth:** 10GbE

---

## Plex Media Server Setup

### Planned Plex Configuration

#### Version
- **Software:** Plex Media Server (latest stable)
- **Platform:** Ubuntu 24.04 LTS
- **Architecture:** x86_64

#### Media Library Structure

```
/mnt/media/
├── Movies/
│   ├── Action/
│   ├── Comedy/
│   ├── Drama/
│   ├── Horror/
│   ├── Sci-Fi/
│   └── [Genre]/
├── TV Shows/
│   ├── [Show Name] (Year)/
│   │   ├── Season 01/
│   │   ├── Season 02/
│   │   └── [Season XX]/
├── Music/
│   ├── [Artist]/
│   │   └── [Album]/
└── Photos/
    └── [Year]/
        └── [Event]/
```

#### File Naming Conventions

**Movies:**
```
Movie Name (Year).ext
Movie Name (Year) - [Quality].ext
Movie Name (Year) - [Edition].ext

Examples:
The Matrix (1999).mkv
Blade Runner (1982) - Director's Cut.mkv
Inception (2010) - 4K.mkv
```

**TV Shows:**
```
Show Name (Year)/Season XX/Show Name - SXXEXX - Episode Title.ext

Examples:
Breaking Bad (2008)/Season 01/Breaking Bad - S01E01 - Pilot.mkv
The Office (2005)/Season 03/The Office - S03E12 - Back From Vacation.mkv
```

**Music:**
```
Artist Name/Album Name (Year)/## - Track Name.ext

Examples:
Pink Floyd/The Dark Side of the Moon (1973)/01 - Speak to Me.flac
Led Zeppelin/Led Zeppelin IV (1971)/04 - Stairway to Heaven.flac
```

### Transcoding Configuration

**Hardware Acceleration:**
- **Method:** Intel Quick Sync Video (QSV) via integrated GPU
- **Purpose:** Efficient real-time transcoding
- **Cache Location:** `/var/lib/plexmediaserver/transcode` (on SSD)
- **Cache Size:** 200GB

**Transcoding Settings:**
- Enable hardware acceleration
- Set transcoder temporary directory to SSD mount
- Enable background transcoding optimization
- Limit simultaneous transcodes based on CPU load

### Remote Access

**External Access:**
- **Method:** Plex Remote Access with Cloudflare tunnel
- **Domain:** TBD (subdomain of lightspeedup.com)
- **SSL:** Let's Encrypt certificate
- **Port Forwarding:** Secured through Cloudflare

---

## File Organization & Naming

### Media Acquisition Guidelines

1. **Quality Standards:**
   - Movies: Prefer 1080p H.264/H.265, 4K for selected titles
   - TV Shows: 1080p H.264/H.265
   - Music: FLAC for archival, MP3 320kbps minimum
   - Subtitles: Include .srt files when available

2. **Metadata Requirements:**
   - Use proper naming conventions for automatic matching
   - Include year in parentheses for movies and TV shows
   - Maintain consistent folder structure
   - Embed metadata when possible

3. **File Formats:**
   - **Video:** MKV (preferred), MP4, AVI
   - **Audio:** FLAC, MP3, AAC, OGG
   - **Subtitles:** SRT, ASS, SUB

### Content Organization Rules

#### Movies
- One folder per movie
- Include trailers, extras in subdirectories if available
- Name format: `Movie Name (Year)/Movie Name (Year).ext`

#### TV Shows
- One folder per series
- Subfolders for each season
- Episode naming: `SXXEXX` format
- Include specials in `Season 00`

#### Music
- Artist folders at root level
- Album folders within artist folders
- Include album art as `folder.jpg`
- Tag files with proper metadata (ID3 tags)

---

## Backup Strategy

### ZFS Snapshots

**Automated Snapshots:**
- **Frequency:** Daily snapshots, retained for 7 days
- **Weekly:** Retained for 4 weeks
- **Monthly:** Retained for 12 months
- **Location:** Same ZFS pool (local snapshots)

**Snapshot Commands:**
```bash
# Create manual snapshot
zfs snapshot Storage_HDD/SethFlix@$(date +%Y%m%d-%H%M%S)

# List snapshots
zfs list -t snapshot

# Rollback to snapshot
zfs rollback Storage_HDD/SethFlix@snapshot-name
```

### Off-Site Backup

**Strategy:** TBD
- **Method:** ZFS send/receive to external location
- **Frequency:** Weekly
- **Retention:** 3 months
- **Encryption:** Enabled for external backups

### Database Backups

**Plex Database:**
- **Location:** `/var/lib/plexmediaserver/Library/Application Support/Plex Media Server/`
- **Backup Frequency:** Daily
- **Retention:** 30 days
- **Method:** Automated script with compression

---

## Performance Optimization

### Transcoding Performance

**CPU Allocation:**
- Reserve 8-12 cores for transcoding
- Enable CPU pinning for consistent performance
- Monitor CPU temperature and throttling

**Storage Optimization:**
- Transcoding cache on SSD (Storage_SSD)
- Media library on HDD (Storage_HDD)
- Enable ZFS prefetch for better read performance
- ARC (cache) tuning for optimal memory usage

### Network Optimization

**Bandwidth Management:**
- 10GbE network for local streaming (no transcoding)
- QoS rules for remote access
- Cloudflare CDN for static content
- Rate limiting for external users

### Database Optimization

**Plex Database:**
- Regular database maintenance (vacuum, analyze)
- Index optimization for faster searches
- Periodic database corruption checks
- SSD storage for database files

---

## Monitoring & Maintenance

### Health Checks

**Automated Monitoring:**
1. **ZFS Health:**
   - `zpool status` - Check pool health
   - `zpool scrub Storage_HDD` - Monthly data integrity check
   - Alert on degraded pools or failing drives

2. **VM Performance:**
   - CPU usage monitoring
   - RAM utilization tracking
   - Disk I/O metrics
   - Network bandwidth monitoring

3. **Plex Metrics:**
   - Active streams
   - Transcoding sessions
   - Library scan status
   - Remote access connectivity

### Maintenance Schedule

**Daily:**
- Automated snapshots
- Log review
- Backup verification

**Weekly:**
- Update checks (OS, Plex, dependencies)
- Disk space review
- Performance metrics analysis

**Monthly:**
- ZFS scrub (data integrity check)
- Security updates
- Capacity planning review
- Documentation updates

---

## Security Considerations

### Access Control

**Physical Security:**
- Server in secure location
- UPS for power protection
- Temperature monitoring

**Network Security:**
- Firewall rules (UFW/iptables)
- SSH key-based authentication only
- Fail2ban for brute force protection
- Regular security audits

**Plex Security:**
- Strong admin password
- Two-factor authentication enabled
- Secure remote access via Cloudflare
- Regular Plex updates for security patches

### Data Protection

**Encryption:**
- ZFS encryption for sensitive datasets (optional)
- HTTPS for all external access
- Encrypted backups for off-site storage

**Privacy:**
- No public sharing without permission
- User access control and authentication
- Activity logging for audit trails

---

## Troubleshooting Guide

### Common Issues

#### Storage Issues

**Problem:** Pool degraded or offline
```bash
# Check pool status
zpool status

# If disk failed, replace and resilver
zpool replace Storage_HDD [old-disk] [new-disk]

# Monitor resilver progress
zpool status -v
```

**Problem:** Out of disk space
```bash
# Check pool usage
zpool list
df -h

# Remove old snapshots
zfs list -t snapshot | grep [old-date]
zfs destroy Storage_HDD/SethFlix@[snapshot-name]
```

#### VM Issues

**Problem:** VM won't start
```bash
# Check VM status
qm list
qm status 200

# View VM logs
tail -f /var/log/pve/qemu-server/200.log

# Start VM manually
qm start 200
```

#### Plex Issues

**Problem:** Transcoding not working
- Verify transcoding cache location
- Check available disk space on SSD
- Ensure hardware acceleration is enabled
- Review Plex logs: `/var/lib/plexmediaserver/Library/Application Support/Plex Media Server/Logs/`

**Problem:** Library not updating
- Trigger manual library scan
- Check file permissions (plex user needs read access)
- Verify NFS/mount points are accessible
- Review Plex scanner logs

---

## Future Roadmap

### Short-Term Goals (1-3 months)
- [ ] Complete Plex VM setup and configuration
- [ ] Migrate existing media to proper folder structure
- [ ] Implement automated backup solution
- [ ] Set up remote access via Cloudflare
- [ ] Configure hardware transcoding
- [ ] Create Plex user accounts and sharing settings

### Medium-Term Goals (3-6 months)
- [ ] Implement monitoring dashboard
- [ ] Set up automated media organization (Sonarr/Radarr)
- [ ] Create content request system
- [ ] Expand storage capacity if needed
- [ ] Implement content retention policies
- [ ] Add 4K HDR support

### Long-Term Goals (6-12 months)
- [ ] Multi-site backup solution
- [ ] Load balancing for multiple streams
- [ ] Advanced analytics and usage tracking
- [ ] Integration with home automation
- [ ] Consider Plex Pass features (DVR, etc.)
- [ ] Explore alternative frontends (Jellyfin as backup)

---

## Technical Reference

### Useful Commands

**ZFS Commands:**
```bash
# Pool status
zpool status
zpool list

# Dataset management
zfs list
zfs create Storage_HDD/SethFlix/Movies
zfs set compression=lz4 Storage_HDD/SethFlix

# Snapshots
zfs snapshot Storage_HDD/SethFlix@backup
zfs list -t snapshot
zfs rollback Storage_HDD/SethFlix@backup

# Scrub (data integrity check)
zpool scrub Storage_HDD
zpool status -v
```

**Proxmox Commands:**
```bash
# VM management
qm list
qm start 200
qm stop 200
qm status 200

# Storage management
pvesm status
pvesm list Storage_HDD

# System info
pveversion
pveperf
```

**Plex Commands:**
```bash
# Service control
sudo systemctl status plexmediaserver
sudo systemctl restart plexmediaserver

# Database optimization
sudo -u plex sqlite3 /var/lib/plexmediaserver/Library/Application\ Support/Plex\ Media\ Server/Plug-in\ Support/Databases/com.plexapp.plugins.library.db "VACUUM;"

# Library scan
sudo -u plex /usr/lib/plexmediaserver/Plex\ Media\ Scanner --scan --refresh --section 1
```

### Configuration Files

**Important Locations:**
- Proxmox config: `/etc/pve/`
- VM configs: `/etc/pve/qemu-server/`
- ZFS config: `/etc/zfs/`
- Plex config: `/var/lib/plexmediaserver/Library/Application Support/Plex Media Server/`
- Network config: `/etc/network/interfaces`

---

## Contact & Support

**Project Maintainer:** Seth (U.S. Marine Corps Veteran)  
**Company:** LightSpeedUp Infrastructure & Hosting  
**Website:** https://lightspeedup.com  
**Email:** support@lightspeedup.com  
**Location:** Warwick, RI

### Community Resources
- **GitHub Repository:** https://github.com/MatoTeziTanka/SethFlix-Plex
- **Issue Tracker:** GitHub Issues
- **Documentation:** This file (README.md equivalent)

---

## License

This project is licensed under the Apache License 2.0. See the LICENSE file for details.

---

## Acknowledgments

- Proxmox team for excellent virtualization platform
- OpenZFS community for robust filesystem
- Plex team for media server software
- Open source community for tools and support

---

**Document Version:** 1.0  
**Last Updated:** November 4, 2025  
**Status:** Active Development  
**Next Review:** December 2025

