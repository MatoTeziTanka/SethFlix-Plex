# Frequently Asked Questions (FAQ)

Common questions about the SethFlix infrastructure and self-hosted media servers.

## General Questions

### What is SethFlix?

SethFlix is a self-hosted media streaming platform built on enterprise-grade hardware using Proxmox virtualization and ZFS storage. It's essentially a personal Netflix/Spotify alternative that you fully control.

### Why self-host instead of using cloud services?

**Advantages:**
- Complete ownership of your media
- No monthly streaming fees (beyond your own costs)
- Better privacy - your viewing habits aren't tracked
- Customization and control
- Learning opportunity for infrastructure skills
- Can share with family/friends

**Disadvantages:**
- Upfront hardware costs
- Ongoing electricity costs
- Maintenance responsibility
- Internet bandwidth for remote streaming
- Legal responsibility for content

### How much does it cost to build something similar?

**Hardware costs (approximate):**
- Used Dell R730: $800-2,000
- Storage drives: $200-2,000 (depends on capacity)
- UPS: $150-500
- Networking gear: $50-500

**Ongoing costs:**
- Electricity: $20-80/month (varies by location and usage)
- Internet: Using existing connection
- Optional Plex Pass: $5/month or $120 lifetime

### Is this legal?

**Legal aspects:**
- Self-hosting a media server: ✅ Legal
- Using Plex software: ✅ Legal
- Hosting your own legally obtained content: ✅ Legal
- Sharing with family in your household: ✅ Generally legal
- **Important**: You are responsible for ensuring your content is legally obtained

## Technical Questions

### Why Proxmox instead of bare metal Plex?

**Benefits of Proxmox:**
- Run multiple services on one server
- Easy VM management and snapshots
- Better resource allocation
- Ability to run other VMs alongside Plex
- Learning experience with virtualization

**Bare metal might be better if:**
- You only want Plex (simpler)
- Maximum performance is critical
- You prefer simplicity over flexibility

### Why ZFS for storage?

**ZFS advantages:**
- Data integrity checksumming (detects silent corruption)
- Snapshots for backups
- Built-in compression
- RAID-like redundancy without hardware RAID cards
- Excellent performance
- Free and open source

**Considerations:**
- Requires more RAM (1GB per TB is recommended)
- Can't easily expand RAIDZ arrays
- Learning curve

### How much storage do I need?

**Media size estimates:**
- 1080p Movie: 4-8GB
- 4K Movie: 15-40GB
- TV Show Episode (1080p): 1-3GB
- Music Album (FLAC): 300-500MB

**Examples:**
- 100 movies (1080p): ~500GB
- 50 TV series (avg 5 seasons): ~500GB-1TB
- Music library: 100-500GB
- **Total for medium library**: 2-3TB

**Recommendation:** Start with 4TB usable, plan for growth

### Can I access my Plex remotely?

Yes! Several options:

1. **Plex Remote Access** (easiest)
   - Built into Plex
   - Requires port forwarding
   - Free with basic Plex account

2. **Cloudflare Tunnel** (most secure)
   - No port forwarding needed
   - Free tier available
   - DDoS protection included

3. **VPN** (most private)
   - Connect to home network
   - Access everything securely
   - Requires VPN server setup

### What about 4K streaming?

**Requirements:**
- Powerful CPU or hardware transcoding
- Fast network (100+ Mbps for remote, 1Gbps for local)
- Plex Pass for hardware transcoding
- 4K media files (larger storage needs)

**Challenges:**
- Transcoding 4K is CPU-intensive
- Large file sizes
- Bandwidth requirements
- Some 4K features (HDR) don't transcode well

**Recommendation:** Use 4K for local streaming only, 1080p for remote

## Hardware Questions

### Do I need a server, or can I use a desktop?

You can use either:

**Server advantages:**
- Built for 24/7 operation
- ECC RAM for data integrity
- Better expansion options
- Remote management (iDRAC, iLO)

**Desktop advantages:**
- Lower cost
- Quieter operation
- Lower power consumption
- Easier to find parts

**Recommendation:** Server if running 24/7 and budget allows, otherwise a good desktop works fine

### What CPU do I need?

**Minimum:**
- Quad-core Intel/AMD
- Passmark score: 2000+ per simultaneous transcode

**Recommended:**
- 6-8 core modern CPU
- Intel with Quick Sync (for hardware transcoding)
- Passmark score: 8000+

**Example use cases:**
- Direct play (no transcode): Any modern CPU
- 1-2 transcodes: Quad-core i5/Ryzen 5
- 3-5 transcodes: 6-8 core Xeon/i7/Ryzen 7
- 5+ transcodes: High-end Xeon or hardware transcoding

### How much RAM do I need?

**Minimums:**
- Proxmox host: 8GB
- Plex VM: 4GB
- ZFS cache: 1GB per TB of storage

**Recommended:**
- Proxmox host: 16GB+
- Plex VM: 8-16GB
- Additional RAM for ZFS ARC cache

**Example:**
- 10TB storage + Proxmox + Plex: 32GB total
- 20TB storage + multiple VMs: 64GB+

## Software Questions

### Which Linux distro for the Plex VM?

**Recommended: Ubuntu Server 24.04 LTS**
- Well-supported by Plex
- LTS = Long-term support
- Familiar to most users
- Large community

**Alternatives:**
- Debian (more stable, less bleeding edge)
- CentOS/Rocky Linux (enterprise-focused)
- Any distro officially supported by Plex

### Do I need Plex Pass?

**Free tier includes:**
- Unlimited libraries
- Unlimited devices
- Mobile apps (limited)
- Basic streaming

**Plex Pass adds ($5/mo or $120 lifetime):**
- Hardware transcoding (critical for 4K or many streams)
- Mobile sync (download for offline)
- Better mobile apps
- Live TV & DVR
- Early access to features

**Recommendation:** Start free, upgrade for hardware transcoding or DVR

### What about Jellyfin or Emby?

**Plex:**
- ✅ Most polished interface
- ✅ Best metadata matching
- ✅ Easiest setup
- ❌ Some features require Plex Pass
- ❌ Closed source

**Jellyfin:**
- ✅ Fully open source
- ✅ All features free
- ✅ No tracking
- ❌ Less polished interface
- ❌ Metadata matching not as good

**Emby:**
- ✅ Good balance
- ❌ Some features premium
- ❌ Less popular than Plex

**Recommendation:** Plex for ease of use, Jellyfin for open source preference

## Media Management

### How should I organize my media?

See the main README for full naming conventions. Quick summary:

**Movies:**
```
/Movies/Movie Name (Year)/Movie Name (Year).mkv
```

**TV Shows:**
```
/TV Shows/Show Name (Year)/Season 01/Show Name - S01E01 - Episode Title.mkv
```

**Music:**
```
/Music/Artist Name/Album Name (Year)/01 - Track Name.flac
```

### What file formats work best?

**Video:**
- Container: MKV (best) or MP4
- Video codec: H.264 (most compatible) or H.265 (better compression)
- Audio codec: AAC, AC3, or DTS

**Audio:**
- Music: FLAC (lossless) or MP3 320kbps
- Movie audio: Whatever came with the file

**Subtitles:**
- SRT (simple text)
- ASS/SSA (styled subtitles)

### How do I automate media management?

Popular tools:
- **Sonarr** - Automatic TV show downloads and organization
- **Radarr** - Automatic movie downloads and organization
- **Lidarr** - Music management
- **Bazarr** - Subtitle management

**Note:** This project doesn't cover these tools yet (see future roadmap).

## Backup Questions

### How should I backup my Plex server?

**Critical data to backup:**
1. Plex database (`/var/lib/plexmediaserver/`)
2. ZFS configurations
3. VM configurations (Proxmox)

**Media backup:**
- Original media should be backed up if irreplaceable
- For legally purchased media, backup is highly recommended
- Consider off-site backup for important content

**Backup strategies:**
- ZFS snapshots (local, quick recovery)
- ZFS send/receive (off-site)
- Rsync to external drive
- Cloud backup (expensive for large media)

### How often should I backup?

**Recommended schedule:**
- Plex database: Daily
- ZFS snapshots: Hourly/Daily
- Off-site: Weekly
- Full system backup: Monthly

## Performance Questions

### Why is transcoding slow?

**Common causes:**
- Insufficient CPU power
- No hardware acceleration enabled
- Multiple simultaneous transcodes
- 4K content (very demanding)

**Solutions:**
- Enable hardware transcoding (Plex Pass)
- Limit simultaneous streams
- Use more compatible formats (reduce need to transcode)
- Upgrade CPU

### How can I improve streaming performance?

1. **Local network:**
   - Use wired connections
   - Upgrade to gigabit switches
   - Enable direct play (no transcoding)

2. **Remote streaming:**
   - Upload bandwidth is critical
   - Optimize transcoding settings
   - Use Cloudflare for caching
   - Pre-optimize files

3. **Storage:**
   - Use SSD for transcoding cache
   - Keep ZFS pools below 80% full
   - Regular scrubbing

## Troubleshooting

### Plex says "Server not found"

**Check:**
1. Is the VM running? `qm status 200`
2. Is Plex service running? `systemctl status plexmediaserver`
3. Network connectivity from client device
4. Firewall rules blocking Plex ports
5. Try local IP: `http://YOUR_PLEX_IP:32400/web`

### Media not showing up in Plex

**Common issues:**
1. File naming doesn't match Plex expectations
2. File permissions (plex user needs read access)
3. Library not pointed to correct folder
4. Metadata agent not matching properly

**Solutions:**
1. Check naming conventions
2. Run: `chown -R plex:plex /media/path`
3. Verify library settings
4. Use Plex "Fix Match" feature

### ZFS pool showing as degraded

**Immediate actions:**
1. Check status: `zpool status`
2. Identify failed drive
3. Don't panic - redundant RAID protects you
4. Order replacement drive
5. Replace and resilver

**See full troubleshooting in main README**

## Cost & Efficiency

### How much power does it use?

**Typical power consumption:**
- Dell R730 idle: 150-200W
- Under load: 300-500W
- Desktop/workstation: 50-150W idle

**Monthly costs (at $0.12/kWh):**
- R730 running 24/7: $20-50/month
- Desktop server: $5-15/month

**Optimization:**
- Use power management features
- Consider newer, more efficient hardware
- Turn off during unused hours (if not 24/7 use)

## Security Questions

### Is self-hosting secure?

**Risks:**
- Open ports can be attack vectors
- Misconfigured security
- Outdated software
- Insider threats (if sharing)

**Mitigations:**
- Use Cloudflare tunnel (no open ports)
- Keep software updated
- Strong passwords and 2FA
- Regular security audits
- Firewall everything
- See SECURITY.md for full details

### Should I share my server with others?

**Considerations:**
- Bandwidth usage
- Privacy concerns
- Legal responsibility
- Resource usage
- Trust level with users

**Best practices if sharing:**
- Only with trusted friends/family
- Set bandwidth limits
- Monitor usage
- Use Plex managed users
- Consider legal implications

## Still Have Questions?

- 📖 Check the main [README.md](../README.md)
- 🐛 Open an [Issue](https://github.com/MatoTeziTanka/SethFlix-Plex/issues)
- 💬 Start a [Discussion](https://github.com/MatoTeziTanka/SethFlix-Plex/discussions)
- 📧 Email: support@lightspeedup.com

---

*Last updated: November 4, 2025*

