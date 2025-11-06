# Changelog

All notable changes to the SethFlix-Plex infrastructure project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html) for documentation versions.

## [Unreleased]

### Planned
- Implement automated backup solution
- Configure hardware transcoding
- Monitoring dashboard implementation
- Automated media organization (Sonarr/Radarr)

---

## [1.1.0] - 2025-11-06

### Added
- **Comprehensive Remote Access Setup Documentation** (`docs/REMOTE_ACCESS_SETUP.md`)
  - Cloudflare Tunnel configuration with full technical details
  - Nginx reverse proxy setup on VM120 (Reverse Proxy Gateway)
  - Plex network security configuration and IP whitelisting
  - Production architecture diagrams and network flow documentation
  - Detailed troubleshooting guide for remote access issues
  - Security best practices and hardening recommendations
  - Performance optimization guidelines for streaming
- **Network Storage Configuration Documentation** (`docs/STORAGE_NETWORK_SETUP.md`)
  - Complete SMB/CIFS setup guide for Proxmox host
  - Windows Server 2025 client configuration and security policies
  - Persistent network drive mapping (P: drive → ZFS storage)
  - Auto-mount on boot configuration with startup scripts
  - Comprehensive troubleshooting for 7 common SMB issues
  - Performance tuning for 10GbE network storage
  - Security hardening and encryption options

### Changed
- Updated README.md with link to detailed remote access documentation
- Enhanced network architecture section with Cloudflare tunnel architecture
- Updated security posture to zero-exposed-ports infrastructure
- Updated docs/README.md to reference new storage network setup guide
- Enhanced documentation structure with SMB/CIFS configuration references

### Completed
- ✅ Plex VM setup and configuration (VM200 on Windows Server 2025 Datacenter)
- ✅ Remote access via Cloudflare Tunnel successfully implemented
- ✅ Production domain: https://plex.lightspeedup.com (live and operational)
- ✅ Network storage configured and mounted (18TB ZFS pool accessible via SMB)
- ✅ Auto-mount on boot configured with startup scripts
- ✅ P: drive persistent mapping verified and tested

### Technical Details
- **VM120:** Ubuntu-based reverse proxy with nginx and cloudflared
- **VM200:** Windows Server 2025 Datacenter with Plex Media Server 1.42.2
- **Cloudflare DNS:** CNAME records configured for tunnel routing
- **Plex Network:** Cloudflare IP ranges whitelisted, custom URL configured
- **Security:** Zero port forwarding, SSL/TLS at edge, DDoS protection active
- **Storage:** Proxmox SMB/CIFS share (SharedFolder) serving /Storage_HDD/shared
- **Capacity:** 18TB total (12TB used, 6TB free) - ZFS pool with LZ4 compression
- **Network Drive:** P: → \\192.168.12.70\SharedFolder (persistent, auto-mount enabled)
- **Performance:** 10GbE network with jumbo frames support, optimized for media streaming

---

## [1.0.0] - 2025-11-04

### Added
- **Initial Release** of comprehensive infrastructure documentation
- Complete hardware specifications for Dell PowerEdge R730
- Network architecture documentation with IP addressing scheme
- Storage architecture details including all ZFS pools
  - Storage_HDD (36.4TB) - Plex media storage
  - Storage_SSD (14TB) - High-performance storage
  - Premium_SSD (668GB) - Premium tier storage
- Virtual machine configuration for 11 VMs including SethFlix-Media-Server-2025 (VM200)
- Plex Media Server setup guide and configuration
- File organization and naming conventions
  - Movies naming standards
  - TV Shows naming standards
  - Music naming standards
- Backup strategy documentation
  - ZFS snapshot policies
  - Database backup procedures
- Performance optimization guidelines
  - Transcoding performance tuning
  - Network optimization
  - Database optimization
- Monitoring and maintenance schedules
- Security considerations and best practices
- Troubleshooting guide for common issues
- Technical reference section with useful commands
- Future roadmap (short, medium, and long-term goals)
- Contact and support information
- Apache 2.0 license

### Documentation Structure
- README.md - Main documentation (647 lines)
- LICENSE - Apache 2.0 license
- CONTRIBUTING.md - Contribution guidelines
- CODE_OF_CONDUCT.md - Community standards
- SECURITY.md - Security policies and reporting
- CHANGELOG.md - This file

### Infrastructure Details

#### Hardware
- Dell PowerEdge R730 server
- Dual Intel Xeon E5-2698 v3 @ 2.30GHz (64 cores)
- 480GB DDR4 ECC RAM
- 10GbE network capability
- Samsung PM883 Enterprise SSDs (4x 3.84TB in RAIDZ2)
- Enterprise HDDs (36.4TB total capacity)

#### Software Stack
- Proxmox Virtual Environment 8.x
- ZFS filesystem for storage
- Ubuntu Server 24.04 LTS (planned for Plex VM)
- Plex Media Server (planned)

#### Network
- Network segment: Example 192.168.1.0/24 (configure for your setup)
- Proxmox host: Configure static IP
- Gateway: Your router IP
- Cloudflare integration for CDN and DDoS protection

### Known Issues
- VM200 (SethFlix-Media-Server-2025) not yet fully configured
- Plex IP address TBD
- Off-site backup strategy TBD
- Remote access not yet implemented

---

## Version History Summary

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2025-11-04 | Initial comprehensive documentation release |

---

## Types of Changes

This changelog uses the following types of changes:

- **Added** - New features or documentation
- **Changed** - Changes to existing functionality or docs
- **Deprecated** - Soon-to-be removed features
- **Removed** - Removed features or documentation
- **Fixed** - Bug fixes or documentation corrections
- **Security** - Security-related changes or updates

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for details on how to contribute to this project.

---

## Links

- [Repository](https://github.com/MatoTeziTanka/SethFlix-Plex)
- [Issues](https://github.com/MatoTeziTanka/SethFlix-Plex/issues)
- [LightSpeedUp Website](https://lightspeedup.com)

---

## Maintainer

**Seth** - U.S. Marine Corps Veteran  
LightSpeedUp Infrastructure & Hosting  
Warwick, RI

---

*Keep a Changelog format used to maintain consistency and readability*  
*Last Updated: November 6, 2025*

