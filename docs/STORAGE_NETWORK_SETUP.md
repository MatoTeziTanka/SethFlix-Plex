# Network Storage Setup Guide

**Guide:** SMB/CIFS Network Storage Configuration  
**Last Updated:** November 6, 2025  
**Infrastructure:** Proxmox → Windows Server 2025  
**Purpose:** Persistent network storage for Plex Media Server

---

## Table of Contents

1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Prerequisites](#prerequisites)
4. [Proxmox Host SMB Configuration](#proxmox-host-smb-configuration)
5. [Windows Server Client Configuration](#windows-server-client-configuration)
6. [Auto-Mount on Boot](#auto-mount-on-boot)
7. [Verification & Testing](#verification--testing)
8. [Troubleshooting](#troubleshooting)
9. [Security Considerations](#security-considerations)
10. [Performance Optimization](#performance-optimization)

---

## Overview

This guide documents the network storage configuration for the SethFlix infrastructure, specifically the SMB/CIFS share mounting between the Proxmox hypervisor and the Windows Server 2025 Plex VM. This setup enables the Plex Media Server to access large media libraries stored on ZFS pools without consuming VM disk space.

### Key Features

- **Protocol:** SMB2/3 (CIFS)
- **Storage Backend:** ZFS on Proxmox host
- **Client OS:** Windows Server 2025 Datacenter
- **Capacity:** 18TB total (12TB used, 6TB free)
- **Auto-Mount:** Configured to survive reboots
- **Security:** Authenticated access with dedicated SMB user

### Use Case

The Plex Media Server VM (VM200) mounts the `/Storage_HDD/shared` directory from the Proxmox host as the `P:` drive, providing access to:
- Movie library
- TV show library
- Recently added media
- Historic backups
- Plex metadata and caches

---

## Architecture

### Storage Flow

```
┌─────────────────────────────────────────┐
│   Proxmox Host (192.168.12.70)         │
│                                          │
│   ZFS Pool: Storage_HDD                 │
│   └── /Storage_HDD/shared (18TB)       │
│        └── Movies/                      │
│        └── Tv Shows/                    │
│        └── Plex Media Server/           │
│        └── Recently Added/              │
│                                          │
│   Samba Server (smbd/nmbd)             │
│   └── Share: "SharedFolder"            │
│        Path: /Storage_HDD/shared        │
│        User: smbuser                    │
│                                          │
└────────────┬────────────────────────────┘
             │ SMB2/3 Protocol
             │ Port 445
             │ 10GbE Network
             ▼
┌─────────────────────────────────────────┐
│   VM200: SethFlix-Media-Server-2025    │
│   OS: Windows Server 2025 Datacenter    │
│   IP: 192.168.12.200                    │
│                                          │
│   P: Drive → \\192.168.12.70\SharedFolder │
│   └── Mapped with persistent credentials│
│                                          │
│   Plex Media Server                     │
│   └── Libraries point to P:\Movies      │
│        └── P:\Tv Shows                  │
│                                          │
└─────────────────────────────────────────┘
```

### Network Details

- **Server IP:** 192.168.12.70 (Proxmox host)
- **Client IP:** 192.168.12.200 (Windows VM)
- **Share Name:** `SharedFolder`
- **Share Path:** `/Storage_HDD/shared`
- **SMB Protocol:** SMB2/3 (SMB1 disabled for security)
- **Authentication:** Username/password (smbuser)

---

## Prerequisites

### On Proxmox Host

- ✅ Samba installed (`apt install samba samba-common-bin`)
- ✅ ZFS pool mounted at `/Storage_HDD/shared`
- ✅ Firewall allows SMB traffic (ports 445, 139)
- ✅ SMB user created and configured

### On Windows Server VM

- ✅ Windows Server 2025 Datacenter
- ✅ Network connectivity to Proxmox host
- ✅ SMB2/3 protocol enabled (default)
- ✅ Administrator access for configuration

### Network Requirements

- ✅ Port 445 (SMB) accessible between VM and host
- ✅ Port 139 (NetBIOS) accessible (optional but recommended)
- ✅ Low latency network (same subnet recommended)

---

## Proxmox Host SMB Configuration

### Step 1: Install Samba

If not already installed:

```bash
# Update package list
apt update

# Install Samba
apt install samba samba-common-bin -y

# Verify installation
samba --version
# Expected: Version 4.17.12-Debian or newer
```

### Step 2: Create SMB User

Create a dedicated user for SMB access:

```bash
# Create system user (if not exists)
useradd -m -s /bin/bash smbuser

# Set system password
echo 'smbuser:YourStrongPassword' | chpasswd

# Add user to Samba database
smbpasswd -a smbuser
# Enter password when prompted: YourStrongPassword
# Retype password: YourStrongPassword

# Enable the Samba user
smbpasswd -e smbuser

# Verify user was added
pdbedit -L
# Expected output: smbuser:1000:Seth (or similar)
```

### Step 3: Configure Samba Share

Edit the Samba configuration file:

```bash
nano /etc/samba/smb.conf
```

Add the following share configuration at the end:

```ini
[SharedFolder]
   comment = ZFS Pool Shared Folder
   path = /Storage_HDD/shared
   browseable = yes
   read only = no
   create mask = 0775
   directory mask = 0775
   valid users = smbuser
   writable = yes
   guest ok = no
   force user = smbuser
```

**Configuration Explanation:**

- `path`: Physical path on the Proxmox host
- `browseable`: Makes share visible when browsing network
- `read only = no`: Allows write access
- `create mask`: Default permissions for new files (rwxrwxr-x)
- `directory mask`: Default permissions for new directories
- `valid users`: Only allow specific user (smbuser)
- `writable`: Confirms write access is allowed
- `guest ok = no`: Disables anonymous guest access
- `force user`: All files created as this user

### Step 4: Set Permissions

Ensure proper ownership and permissions:

```bash
# Set ownership to SMB user
chown -R smbuser:smbuser /Storage_HDD/shared

# Set permissions (755 = rwxr-xr-x)
chmod -R 755 /Storage_HDD/shared

# Verify permissions
ls -la /Storage_HDD/
# Expected: drwxr-xr-x ... smbuser smbuser ... shared
```

### Step 5: Configure Firewall

Allow SMB traffic through the firewall:

```bash
# Allow SMB file sharing
ufw allow 445/tcp
ufw allow 139/tcp

# Reload firewall
ufw reload

# Verify rules
ufw status | grep -E '445|139'
```

### Step 6: Restart Samba Services

Apply the configuration:

```bash
# Restart Samba services
systemctl restart smbd nmbd

# Enable auto-start on boot
systemctl enable smbd nmbd

# Check service status
systemctl status smbd
# Expected: active (running)

systemctl status nmbd
# Expected: active (running)
```

### Step 7: Verify Share is Available

Test the share locally:

```bash
# List available shares
smbclient -L localhost -U smbuser
# Enter password when prompted

# Expected output should include:
# Sharename       Type      Comment
# ---------       ----      -------
# SharedFolder    Disk      ZFS Pool Shared Folder
```

---

## Windows Server Client Configuration

### Step 1: Enable SMB Client Features

Open PowerShell as Administrator on VM200:

```powershell
# Check SMB1 status (should be disabled)
Get-WindowsFeature FS-SMB1
# Expected: Install State = Available (not installed)

# Verify SMB2/3 is enabled (should be by default)
Get-SmbServerConfiguration | Select-Object EnableSMB2Protocol
# Expected: True
```

### Step 2: Configure Security Policies

Windows Server 2025 has strict SMB security policies. Configure them:

```powershell
# Allow NTLMv2 authentication (secure)
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Lsa" -Name "LmCompatibilityLevel" -Value 3

# Enable SMB2/3 protocol (should already be enabled)
Set-SmbServerConfiguration -EnableSMB2Protocol $true -Force

# Allow insecure guest authentication (required for some SMB servers)
# Note: Only needed if server doesn't support strict security
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" -Name "AllowInsecureGuestAuth" -Value 1

# Update Group Policy
gpupdate /force

# Restart Workstation service to apply changes
Restart-Service LanmanWorkstation -Force
```

### Step 3: Test SMB Port Connectivity

Verify network connectivity to the SMB server:

```powershell
# Test SMB port (445)
Test-NetConnection -ComputerName 192.168.12.70 -Port 445

# Expected output:
# ComputerName     : 192.168.12.70
# RemoteAddress    : 192.168.12.70
# RemotePort       : 445
# InterfaceAlias   : Ethernet
# SourceAddress    : 192.168.12.200
# TcpTestSucceeded : True
```

**If TcpTestSucceeded is False:**
- Check Proxmox firewall configuration
- Verify Samba service is running on Proxmox
- Check network connectivity between VMs

### Step 4: Map Network Drive

Map the P: drive to the SMB share:

```powershell
# Method 1: Using net use command
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes

# Method 2: Using GUI
# 1. Open File Explorer
# 2. Right-click "This PC"
# 3. Select "Map network drive..."
# 4. Choose drive letter: P:
# 5. Folder: \\192.168.12.70\SharedFolder
# 6. Check "Connect using different credentials"
# 7. Enter username: smbuser
# 8. Enter password: YourPassword
# 9. Check "Remember my credentials"
# 10. Click Finish
```

**Expected output:**

```
The command completed successfully.
```

### Step 5: Verify Drive Mount

Confirm the P: drive is accessible:

```powershell
# Check if P: drive exists
Test-Path P:\
# Expected: True

# List drives
Get-PSDrive P

# Expected output:
# Name Root           Used          Free
# ---- ----           ----          ----
# P    P:\  12725460926464 6508479774720

# List files on P: drive
dir P:\ | Select-Object Name, LastWriteTime

# Expected: Shows folders like Movies, Tv Shows, Plex Media Server, etc.
```

### Step 6: Test Read/Write Access

Verify you can create and modify files:

```powershell
# Create a test file
echo "SethFlix Network Storage Test" > P:\test.txt

# Read the test file
Get-Content P:\test.txt
# Expected: SethFlix Network Storage Test

# Delete the test file
Remove-Item P:\test.txt

# Verify deletion
Test-Path P:\test.txt
# Expected: False
```

---

## Auto-Mount on Boot

To ensure the P: drive automatically mounts after Windows reboots, create a startup script.

### Method 1: Startup Script (Recommended)

Create a batch file that runs at startup:

**Step 1: Create the batch file**

On VM200, create `C:\Scripts\mount-p-drive.bat`:

```batch
@echo off
REM Wait for network to initialize
timeout /t 10 /nobreak

REM Delete any existing mapping
net use P: /delete /yes

REM Map the P: drive with credentials
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes

REM Log success
echo P: drive mapped successfully at %date% %time% >> C:\Scripts\mount-p-drive.log
```

**Step 2: Place in Startup folder**

Copy the batch file to the startup folder:

```powershell
# Create Scripts directory
New-Item -Path "C:\Scripts" -ItemType Directory -Force

# Create the batch file
@"
@echo off
timeout /t 10 /nobreak
net use P: /delete /yes
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes
echo P: drive mapped at %date% %time% >> C:\Scripts\mount-p-drive.log
"@ | Out-File -FilePath "C:\Scripts\mount-p-drive.bat" -Encoding ASCII

# Copy to Startup folder for current user
Copy-Item "C:\Scripts\mount-p-drive.bat" -Destination "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\"

# Verify it was copied
Test-Path "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\mount-p-drive.bat"
# Expected: True
```

### Method 2: Scheduled Task (Alternative)

Create a scheduled task that runs at logon:

```powershell
# Create scheduled task action
$action = New-ScheduledTaskAction -Execute "net.exe" -Argument "use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes"

# Create trigger (at logon)
$trigger = New-ScheduledTaskTrigger -AtLogOn

# Create task settings
$settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries -StartWhenAvailable

# Register the scheduled task
Register-ScheduledTask -TaskName "Mount P Drive" -Action $action -Trigger $trigger -Settings $settings -User "SYSTEM" -RunLevel Highest -Description "Automatically mount P: drive on boot"

# Verify task was created
Get-ScheduledTask -TaskName "Mount P Drive"
```

### Method 3: Persistent Connection (Built-in)

Windows should remember the connection if `/persistent:yes` was used:

```powershell
# Check persistent connections
net use

# Expected output should show:
# Status       Local     Remote                    Network
# -------------------------------------------------------------------------------
# OK           P:        \\192.168.12.70\SharedFolder
#                                                 Microsoft Windows Network
```

**Note:** The `/persistent:yes` flag should maintain the connection across reboots, but a startup script provides additional reliability.

---

## Verification & Testing

### Daily Operations Check

```powershell
# 1. Check drive status
Get-PSDrive P | Format-Table Name, Root, Used, Free

# 2. Check network connection
Test-NetConnection -ComputerName 192.168.12.70 -Port 445

# 3. Verify Plex can access media
Test-Path P:\Movies
Test-Path P:\"Tv Shows"

# 4. Check for mount errors
Get-EventLog -LogName System -Source LanmanWorkstation -Newest 20 | Where-Object {$_.EntryType -eq "Error"}
```

### Post-Reboot Verification

After rebooting VM200:

1. Wait 30 seconds for network initialization
2. Open PowerShell as Administrator
3. Run verification commands:

```powershell
# Check if P: drive is mounted
Test-Path P:\
# Expected: True

# Check startup script log
Get-Content C:\Scripts\mount-p-drive.log -Tail 5

# Verify Plex service started
Get-Service "Plex Media Server"
# Expected: Status = Running
```

### Performance Testing

Test read/write performance:

```powershell
# Create a large test file (1GB)
$testFile = "P:\speed_test.tmp"
$data = New-Object byte[] (1GB)
[System.IO.File]::WriteAllBytes($testFile, $data)

# Measure write speed
Measure-Command { 
    $data = New-Object byte[] (100MB)
    [System.IO.File]::WriteAllBytes("P:\write_test.tmp", $data)
}

# Measure read speed
Measure-Command {
    $readData = [System.IO.File]::ReadAllBytes("P:\write_test.tmp")
}

# Clean up
Remove-Item "P:\speed_test.tmp", "P:\write_test.tmp"
```

**Expected Performance:**
- **Write Speed:** 100+ MB/s on 10GbE network
- **Read Speed:** 100+ MB/s on 10GbE network
- **Latency:** < 1ms on local network

---

## Troubleshooting

### Issue 1: "Drive is Unavailable" or "Cannot Find Drive"

**Symptoms:**
- `net use` shows status "Unavailable"
- `Test-Path P:\` returns False

**Diagnosis:**

```powershell
# Check network connectivity
Test-NetConnection -ComputerName 192.168.12.70 -Port 445

# Check if drive mapping exists but is disconnected
net use
```

**Solution:**

```powershell
# Delete and remap the drive
net use P: /delete /yes
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes

# Verify
Test-Path P:\
```

**Root Causes:**
- Network interruption
- Samba service restarted on Proxmox
- SMB timeout
- Credentials expired

---

### Issue 2: "Access Denied" or "Permission Denied"

**Symptoms:**
- Can browse P: drive but cannot create files
- Error when saving to P: drive

**Diagnosis:**

```powershell
# Check current permissions
icacls P:\
```

**Solution on Proxmox Host:**

```bash
# Fix ownership
chown -R smbuser:smbuser /Storage_HDD/shared

# Fix permissions (775 = rwxrwxr-x)
chmod -R 775 /Storage_HDD/shared

# Restart Samba
systemctl restart smbd
```

**Solution on Windows:**

```powershell
# Remap with explicit credentials
net use P: /delete
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes
```

---

### Issue 3: "Network Path Not Found"

**Symptoms:**
- Error: "Windows cannot access \\192.168.12.70\SharedFolder"
- `TcpTestSucceeded : False`

**Diagnosis:**

```powershell
# Test network connectivity
Test-NetConnection -ComputerName 192.168.12.70 -Port 445

# Ping the host
ping 192.168.12.70

# Check routing
tracert 192.168.12.70
```

**Solution on Proxmox Host:**

```bash
# Check if Samba is running
systemctl status smbd

# If not running, start it
systemctl start smbd nmbd

# Check firewall
ufw status | grep 445

# If blocked, allow it
ufw allow 445/tcp
ufw reload

# Check if share exists
smbclient -L localhost -U smbuser
```

---

### Issue 4: Slow Performance

**Symptoms:**
- File transfers are slow (< 10 MB/s)
- Plex buffering issues

**Diagnosis:**

```powershell
# Check SMB version in use
Get-SmbConnection | Select-Object ServerName, Dialect

# Expected: Dialect = 3.1.1 or 3.0
```

**Solution:**

```powershell
# Ensure SMB3 is being used
Set-SmbClientConfiguration -RequireSecuritySignature $false -Force

# Disable packet signing (reduces CPU overhead, less secure)
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" -Name "RequireSecuritySignature" -Value 0

# Restart service
Restart-Service LanmanWorkstation -Force
```

**Check Network:**

```bash
# On Proxmox, verify 10GbE is active
ethtool vmbr0 | grep Speed
# Expected: Speed: 10000Mb/s
```

---

### Issue 5: Drive Not Mounting After Reboot

**Symptoms:**
- After reboot, P: drive is not available
- Startup script didn't run

**Diagnosis:**

```powershell
# Check if startup script exists
Test-Path "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\mount-p-drive.bat"

# Check startup script log
Get-Content C:\Scripts\mount-p-drive.log -Tail 10
```

**Solution:**

```powershell
# Verify startup script was created properly
Get-Content "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\mount-p-drive.bat"

# If missing, recreate it
@"
@echo off
timeout /t 10 /nobreak
net use P: /delete /yes
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes
echo P: drive mapped at %date% %time% >> C:\Scripts\mount-p-drive.log
"@ | Out-File -FilePath "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\mount-p-drive.bat" -Encoding ASCII

# Test manually
& "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\mount-p-drive.bat"
```

---

### Issue 6: "System Error 1219"

**Symptoms:**
- Error: "Multiple connections to a server or shared resource by the same user, using more than one user name, are not allowed."

**Solution:**

```powershell
# Disconnect all connections to the server
net use \\192.168.12.70 /delete /yes

# Wait 5 seconds
Start-Sleep -Seconds 5

# Reconnect with correct credentials
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes
```

---

### Issue 7: SMB1 Disabled Errors

**Symptoms:**
- Error: "You can't access this shared folder because your organization's security policies block unauthenticated guest access"

**Solution:**

```powershell
# Allow insecure guest authentication (required for SMB2/3 with basic auth)
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters" -Name "AllowInsecureGuestAuth" -Value 1

# Restart service
Restart-Service LanmanWorkstation -Force

# Retry connection
net use P: \\192.168.12.70\SharedFolder /user:smbuser YourPassword /persistent:yes
```

---

## Security Considerations

### Authentication

- **User-based authentication:** All access requires smbuser credentials
- **No guest access:** `guest ok = no` in smb.conf
- **Password strength:** Use strong passwords (minimum 16 characters)
- **Credential storage:** Windows Credential Manager stores encrypted credentials

### Network Security

- **Internal network only:** SMB should never be exposed to the internet
- **Firewall rules:** Only allow SMB from trusted VM subnet
- **SMB1 disabled:** Prevent known security vulnerabilities
- **Encryption:** SMB3 supports encryption (enable if needed)

### Best Practices

1. **Regular updates:** Keep Samba and Windows Server patched
2. **Audit logs:** Monitor Samba logs for unauthorized access attempts
3. **Least privilege:** Only grant necessary permissions
4. **Rotate credentials:** Change smbuser password periodically
5. **Backup configurations:** Keep backups of smb.conf

### Enable SMB Encryption (Optional)

For maximum security, enable SMB encryption:

**On Proxmox (smb.conf):**

```ini
[global]
   smb encrypt = required

[SharedFolder]
   smb encrypt = required
```

**On Windows:**

```powershell
# Require encryption for SMB connections
Set-SmbClientConfiguration -EncryptionCipherSuite AES256 -Force
```

### Firewall Rules (UFW on Proxmox)

Restrict SMB access to specific IPs:

```bash
# Remove default allow rule
ufw delete allow 445/tcp

# Only allow from VM subnet
ufw allow from 192.168.12.0/24 to any port 445 proto tcp

# Reload firewall
ufw reload
```

---

## Performance Optimization

### SMB Server Tuning (Proxmox)

Edit `/etc/samba/smb.conf` in the `[global]` section:

```ini
[global]
   # Increase buffer sizes for better throughput
   socket options = TCP_NODELAY IPTOS_LOWDELAY SO_RCVBUF=131072 SO_SNDBUF=131072
   
   # Use sendfile for better performance
   use sendfile = yes
   
   # Enable async I/O
   aio read size = 16384
   aio write size = 16384
   
   # Optimize for large file transfers
   min receivefile size = 16384
   
   # Disable unnecessary features
   dns proxy = no
   disable netbios = yes
```

Restart Samba after changes:

```bash
systemctl restart smbd
```

### Windows Client Tuning

```powershell
# Increase SMB buffer sizes
Set-SmbClientConfiguration -SessionTimeout 60 -Force

# Disable SMB signing (reduces CPU, less secure)
Set-SmbClientConfiguration -RequireSecuritySignature $false -Force

# Enable large MTU
Set-NetAdapterAdvancedProperty -Name "Ethernet" -DisplayName "Jumbo Packet" -DisplayValue "9014 Bytes"
```

### Network Optimization

**On Proxmox (10GbE interface):**

```bash
# Enable jumbo frames
ip link set vmbr0 mtu 9000

# Make permanent (edit /etc/network/interfaces)
nano /etc/network/interfaces
# Add: mtu 9000

# Verify
ip link show vmbr0 | grep mtu
```

**On Windows VM:**

```powershell
# Enable jumbo frames
Set-NetAdapterAdvancedProperty -Name "Ethernet" -DisplayName "Jumbo Packet" -DisplayValue "9000"

# Verify
Get-NetAdapterAdvancedProperty -Name "Ethernet" | Where-Object DisplayName -like "*Jumbo*"
```

### ZFS Tuning

For optimal SMB performance with ZFS:

```bash
# Increase ARC (Adaptive Replacement Cache) size
echo "options zfs zfs_arc_max=34359738368" >> /etc/modprobe.d/zfs.conf
# 32GB ARC for 480GB RAM system

# Enable synchronous writes for Samba
zfs set sync=standard Storage_HDD/shared

# Set record size for media files
zfs set recordsize=1M Storage_HDD/shared

# Enable compression
zfs set compression=lz4 Storage_HDD/shared
```

---

## Summary

### Configuration Checklist

- ✅ Samba installed on Proxmox host
- ✅ SMB user (smbuser) created and enabled
- ✅ Share configured in `/etc/samba/smb.conf`
- ✅ Permissions set on `/Storage_HDD/shared`
- ✅ Firewall allows SMB traffic (port 445)
- ✅ Windows Server security policies configured
- ✅ P: drive mapped with persistent credentials
- ✅ Auto-mount script created in Startup folder
- ✅ Verification tests passed
- ✅ Performance optimized

### Key Files and Locations

| Location | File/Directory | Purpose |
|----------|----------------|---------|
| Proxmox | `/etc/samba/smb.conf` | Samba configuration |
| Proxmox | `/Storage_HDD/shared` | Shared storage path |
| Windows | `P:\` | Mapped network drive |
| Windows | `C:\Scripts\mount-p-drive.bat` | Auto-mount script |
| Windows | `%APPDATA%\...\Startup\mount-p-drive.bat` | Startup script location |

### Quick Reference Commands

**Proxmox:**

```bash
# Check Samba status
systemctl status smbd

# List Samba users
pdbedit -L

# Test share access
smbclient -L localhost -U smbuser

# View Samba logs
tail -f /var/log/samba/log.smbd
```

**Windows:**

```powershell
# Check P: drive status
Get-PSDrive P

# Test connectivity
Test-NetConnection -ComputerName 192.168.12.70 -Port 445

# View mounted drives
net use

# Remount P: drive
net use P: \\192.168.12.70\SharedFolder /user:smbuser password /persistent:yes
```

---

## Support and Troubleshooting

For issues not covered in this guide:

1. **Check logs:** `/var/log/samba/log.smbd` on Proxmox
2. **Windows Event Viewer:** Application and System logs
3. **GitHub Issues:** [SethFlix-Plex Issues](https://github.com/MatoTeziTanka/SethFlix-Plex/issues)
4. **Email Support:** support@lightspeedup.com

---

**Guide Maintained By:** Seth - LightSpeedUp Infrastructure  
**Infrastructure:** Warwick, RI  
**Last Verified:** November 6, 2025

---

*This guide is part of the SethFlix-Plex open-source documentation project.*  
*Licensed under Apache 2.0 - See [LICENSE](../LICENSE) for details.*

