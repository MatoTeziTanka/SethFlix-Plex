# Getting Started with SethFlix Infrastructure

This guide will help you understand and potentially replicate the SethFlix infrastructure for your own self-hosted media server setup.

## Prerequisites

Before diving in, you should have:

### Knowledge Requirements
- Basic Linux system administration
- Understanding of virtualization concepts
- Familiarity with networking (IP addressing, routing)
- Basic understanding of storage concepts (RAID, filesystems)
- Command-line comfort

### Hardware Requirements
- Server-grade hardware or a powerful desktop
  - Minimum: Quad-core CPU, 16GB RAM, 4TB storage
  - Recommended: Multi-core Xeon/EPYC, 64GB+ RAM, 10TB+ storage
- Reliable network connection
- UPS (highly recommended)

### Software/Accounts
- Proxmox VE ISO (free download)
- Plex Media Server account (free tier available)
- Domain name (optional, for remote access)
- Cloudflare account (optional, for CDN/security)

## Overview of the Stack

The SethFlix infrastructure is built on three main layers:

1. **Hardware Layer**: Dell PowerEdge R730 server
2. **Virtualization Layer**: Proxmox VE
3. **Application Layer**: Plex Media Server in a VM

```
┌─────────────────────────────────────────┐
│     Plex Media Server (Application)     │
├─────────────────────────────────────────┤
│      Ubuntu VM (Operating System)       │
├─────────────────────────────────────────┤
│       Proxmox VE (Hypervisor)          │
├─────────────────────────────────────────┤
│    Dell PowerEdge R730 (Hardware)       │
└─────────────────────────────────────────┘
```

## Quick Start Guide

### Phase 1: Hardware Setup

1. **Obtain suitable hardware**
   - Server or workstation with virtualization support
   - Multiple drives for storage redundancy
   - Network connectivity (1GbE minimum, 10GbE ideal)

2. **Install Proxmox VE**
   - Download from https://www.proxmox.com/
   - Create bootable USB
   - Install on bare metal
   - Configure network and storage

3. **Configure ZFS pools** (if using ZFS)
   - Create pools with appropriate RAID levels
   - Set up datasets for different purposes
   - Enable compression and snapshots

### Phase 2: Network Configuration

1. **Plan your IP scheme**
   - Choose a private subnet (e.g., 192.168.x.0/24)
   - Reserve IPs for VMs
   - Document your network layout

2. **Configure Proxmox networking**
   - Set up bridge interface
   - Configure firewall rules
   - Set up VLANs if needed

3. **DNS and routing**
   - Configure local DNS (optional)
   - Set up port forwarding for remote access
   - Consider VPN for secure access

### Phase 3: VM Creation

1. **Create the Plex VM**
   - Ubuntu Server 24.04 LTS recommended
   - Allocate appropriate resources:
     - CPU: 4-8 cores minimum
     - RAM: 8-16GB minimum
     - Storage: 100GB for OS, mount media storage separately

2. **Configure storage mounting**
   - Mount ZFS datasets into VM
   - Set proper permissions
   - Verify read/write access

3. **Install and configure Plex**
   - Add Plex repository
   - Install Plex Media Server
   - Complete initial setup via web interface

### Phase 4: Media Organization

1. **Create folder structure**
   - Follow naming conventions (see main README)
   - Organize by media type (Movies, TV, Music)
   - Use consistent naming

2. **Add media to Plex**
   - Create Plex libraries
   - Point to media folders
   - Let Plex scan and match media

3. **Configure transcoding**
   - Set up hardware acceleration if available
   - Configure transcoding cache location
   - Set quality preferences

### Phase 5: Hardening & Optimization

1. **Security**
   - Enable firewall
   - Set up fail2ban
   - Configure SSH keys
   - Enable 2FA on Plex

2. **Backups**
   - Set up ZFS snapshots
   - Configure Plex database backups
   - Test restore procedures

3. **Monitoring**
   - Set up basic monitoring
   - Configure alerts
   - Document baseline performance

## Common Paths

### Path 1: Home Lab Enthusiast
If you're building a home lab:
1. Start with Proxmox on any compatible hardware
2. Create multiple VMs for learning
3. Add Plex as one of many services
4. Experiment with different configurations

### Path 2: Dedicated Media Server
If you're focused solely on media streaming:
1. Install Plex directly on bare metal (simpler)
2. Or use a lightweight VM host (Proxmox)
3. Prioritize storage and network performance
4. Focus on media organization

### Path 3: Learning Infrastructure
If you're learning enterprise concepts:
1. Follow the full SethFlix setup
2. Document everything
3. Practice backups and recovery
4. Implement monitoring and alerting

## Next Steps

After completing this guide:

1. **Read the main README.md** for detailed specifications
2. **Review the security guide** (SECURITY.md)
3. **Set up your first library** and test streaming
4. **Configure remote access** securely
5. **Implement backups** before adding important media
6. **Join the community** and share your experience

## Learning Resources

### Proxmox
- Official Documentation: https://pve.proxmox.com/pve-docs/
- Proxmox VE Tutorial: https://www.youtube.com/proxmox
- Community Forums: https://forum.proxmox.com/

### ZFS
- OpenZFS Documentation: https://openzfs.github.io/openzfs-docs/
- ZFS on Linux: https://zfsonlinux.org/
- Aaron Toponce's ZFS Guide: https://pthree.org/zfs/

### Plex
- Official Support: https://support.plex.tv/
- Plex Media Server Setup: https://support.plex.tv/articles/200288586-installation/
- r/PleX: https://www.reddit.com/r/PleX/

### Networking
- Cloudflare Tunnels: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/
- Port Forwarding Guide: Various by router

## Troubleshooting

### Can't access Proxmox web interface
- Check network connectivity
- Verify IP address: `ip addr show`
- Ensure port 8006 is accessible
- Check firewall rules

### VM won't start
- Check resource availability (RAM, storage)
- Review VM logs: `/var/log/pve/qemu-server/`
- Verify storage is online: `zpool status`

### Plex not finding media
- Check file permissions
- Verify mount points
- Ensure naming follows conventions
- Check Plex logs

## Support

Need help?
- Check the main [README.md](../README.md)
- Review [Troubleshooting Guide](../README.md#troubleshooting-guide)
- Open an [Issue](https://github.com/MatoTeziTanka/SethFlix-Plex/issues)
- Contact: support@lightspeedup.com

---

**Ready to build your own SethFlix?** Let's go! 🚀

