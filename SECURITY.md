# Security Policy

## Supported Versions

This project documents a self-hosted infrastructure setup. Security considerations apply to the infrastructure itself rather than versioned software releases.

| Component | Version | Status |
| --- | --- | --- |
| Proxmox VE | 8.x | ✅ Actively maintained |
| Plex Media Server | Latest stable | ✅ Actively maintained |
| ZFS | Latest stable | ✅ Actively maintained |
| Ubuntu Server | 24.04 LTS | ✅ Actively maintained |

## Reporting a Vulnerability

We take security seriously. If you discover a security vulnerability in our documentation, configurations, or setup recommendations, please report it responsibly.

### How to Report

**Do NOT** create a public GitHub issue for security vulnerabilities.

Instead, please report security issues via:

- **Email**: support@lightspeedup.com
- **Subject Line**: `[SECURITY] SethFlix-Plex - Brief Description`

### What to Include

Please include as much information as possible:

1. **Description** of the vulnerability
2. **Steps to reproduce** the issue
3. **Potential impact** of the vulnerability
4. **Suggested fix** (if you have one)
5. **Your contact information** for follow-up

### Example Security Report

```
Subject: [SECURITY] SethFlix-Plex - Exposed Credentials in Documentation

Description:
The documentation in docs/setup-guide.md contains an example configuration
that uses default passwords without warning users to change them.

Location:
- File: docs/setup-guide.md
- Lines: 42-45

Impact:
Users following this guide may inadvertently use default credentials,
leaving their systems vulnerable to unauthorized access.

Suggested Fix:
Add a prominent warning about changing default passwords and use
placeholder values like "YOUR_PASSWORD_HERE" instead of actual defaults.

Contact: security@example.com
```

## Response Timeline

- **Initial Response**: Within 48 hours of report
- **Status Update**: Within 5 business days
- **Resolution Timeline**: Varies based on severity
  - **Critical**: 1-7 days
  - **High**: 7-14 days
  - **Medium**: 14-30 days
  - **Low**: 30-60 days

## Security Best Practices

This documentation project promotes the following security practices:

### Infrastructure Security

1. **Firewall Configuration**
   - Enable UFW/iptables on all VMs
   - Only open required ports
   - Use Cloudflare for external access
   - Implement rate limiting

2. **Access Control**
   - SSH key-based authentication only
   - Disable password authentication
   - Use Fail2ban for brute force protection
   - Regular security audits

3. **Network Segmentation**
   - Isolate management interfaces
   - Use VLANs where appropriate
   - Implement network-level access controls
   - Monitor network traffic

4. **Data Protection**
   - Enable ZFS encryption for sensitive data
   - Use HTTPS for all web interfaces
   - Encrypt backups at rest
   - Regular backup testing

### Plex-Specific Security

1. **Authentication**
   - Strong admin passwords (16+ characters)
   - Enable two-factor authentication
   - Limit user access appropriately
   - Regular password rotation

2. **Network Security**
   - Secure remote access via Cloudflare tunnel
   - Use reverse proxy with SSL/TLS
   - Disable insecure protocols
   - Regular Plex updates

3. **Content Security**
   - Proper file permissions (plex user only)
   - No public sharing without authentication
   - Activity logging enabled
   - Regular access audits

### Proxmox Security

1. **Web Interface**
   - HTTPS only (disable HTTP)
   - Strong passwords
   - Two-factor authentication
   - IP whitelisting where possible

2. **VM Security**
   - Regular OS updates
   - Minimal installed packages
   - Proper firewall rules per VM
   - Isolated networks where appropriate

3. **Storage Security**
   - Encrypted datasets for sensitive data
   - Proper permissions on storage
   - Regular integrity checks (zpool scrub)
   - Secure backup locations

## Security Updates

We recommend the following update schedule:

### Daily/Automated
- OS security patches (unattended-upgrades)
- Antivirus/malware definitions (if applicable)

### Weekly
- Plex Media Server updates
- Docker containers (if used)
- Application updates

### Monthly
- Proxmox updates (after testing)
- Firmware updates (after testing)
- Security configuration review

### Quarterly
- Full security audit
- Credential rotation
- Access review
- Documentation review

## Known Security Considerations

### Public Documentation

This is a public repository containing infrastructure documentation. We are careful to:

- ❌ **Never include** actual passwords, API keys, or tokens
- ❌ **Never include** actual internal IP addresses (we use example IPs)
- ❌ **Never include** sensitive configuration details
- ✅ **Always use** placeholder values in examples
- ✅ **Always warn** about security implications
- ✅ **Always recommend** best practices

### Community Contributions

When contributing:

- Review your submissions for sensitive information
- Use placeholders for private data
- Redact IP addresses, hostnames, and credentials
- Consider security implications of shared configurations

## Disclosure Policy

### Responsible Disclosure

When we confirm a security issue:

1. We will acknowledge receipt of your report
2. We will work on a fix and document the issue privately
3. We will coordinate disclosure timing with the reporter
4. We will credit the reporter (unless they prefer to remain anonymous)
5. We will update documentation and notify users

### Public Disclosure

After a fix is available:

- Security advisory published in repository
- CHANGELOG.md updated with security notice
- Users notified via GitHub and documentation
- Reporter credited (if desired)

## Security Hall of Fame

We recognize and thank security researchers who responsibly disclose vulnerabilities:

*No vulnerabilities reported yet*

---

## Additional Resources

- [Proxmox Security Best Practices](https://pve.proxmox.com/wiki/Security)
- [Plex Security Recommendations](https://support.plex.tv/articles/200289506-remote-access/)
- [ZFS Security Features](https://openzfs.github.io/openzfs-docs/)
- [Ubuntu Security](https://ubuntu.com/security)

## Contact

For security concerns: support@lightspeedup.com  
For general inquiries: Create a GitHub Issue

---

**Thank you for helping keep SethFlix-Plex infrastructure secure!** 🔒

