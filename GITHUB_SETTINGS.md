# GitHub Repository Settings Guide

This document provides the recommended settings for the SethFlix-Plex repository on GitHub's web interface. These settings are configured through the GitHub web interface, not through git commands.

## Repository Settings to Configure

### 1. Repository Description

Navigate to: **Repository → About (click ⚙️ gear icon)**

**Recommended Description:**
```
Self-hosted media streaming infrastructure built on Dell PowerEdge R730, Proxmox VE, and Plex Media Server. Complete documentation for enterprise-grade home media server setup with ZFS storage.
```

### 2. Website URL

**Recommended URL:**
```
https://lightspeedup.com
```

### 3. Topics/Tags

Add these topics to improve discoverability:

```
plex
proxmox
self-hosted
media-server
home-lab
zfs
dell-poweredge
infrastructure
virtualization
homelab
plex-media-server
streaming
nas
storage
r730
documentation
```

**To add topics:**
1. Go to repository main page
2. Click ⚙️ gear icon next to "About"
3. Add topics in the "Topics" field
4. Click "Save changes"

### 4. Repository Settings

Navigate to: **Settings → General**

#### Features to Enable:
- ✅ **Issues** - For bug reports and feature requests
- ✅ **Discussions** - For community Q&A
- ✅ **Projects** - For roadmap tracking (optional)
- ✅ **Preserve this repository** - Archive with GitHub Archive Program
- ✅ **Sponsorships** - If you want to accept sponsorships
- ✅ **Wikis** - For additional documentation (optional)

#### Pull Requests Settings:
- ✅ **Allow merge commits**
- ✅ **Allow squash merging**
- ✅ **Allow rebase merging**
- ✅ **Always suggest updating pull request branches**
- ✅ **Automatically delete head branches**

### 5. Branch Protection Rules

Navigate to: **Settings → Branches → Add rule**

**For `main` branch:**

Branch name pattern: `main`

Enable:
- ✅ **Require pull request reviews before merging** (if collaborating)
- ✅ **Require status checks to pass before merging** (if using CI/CD)
- ✅ **Require branches to be up to date before merging**
- ✅ **Include administrators** (enforce for everyone)

### 6. Social Preview Image

Navigate to: **Settings → General → Social preview**

**Recommendation:**
Upload a custom image (1280x640 pixels) with:
- SethFlix logo/branding
- Text: "Self-Hosted Media Server Infrastructure"
- Dell PowerEdge R730 image or server rack visual
- Professional, tech-focused design

**Placeholder until custom image:**
GitHub will auto-generate from repository name and description.

### 7. Repository Visibility

Current setting: **Public** ✅

Keep public to share knowledge and help the community.

### 8. GitHub Pages (Optional)

Navigate to: **Settings → Pages**

If you want to host documentation as a website:
- **Source:** Deploy from a branch
- **Branch:** `main`
- **Folder:** `/docs`
- **Custom domain:** (optional) docs.lightspeedup.com

### 9. Security Settings

Navigate to: **Settings → Security**

Enable:
- ✅ **Dependency graph**
- ✅ **Dependabot alerts**
- ✅ **Dependabot security updates**
- ✅ **Private vulnerability reporting**

### 10. Actions Settings (If using GitHub Actions)

Navigate to: **Settings → Actions → General**

Recommended:
- **Actions permissions:** Allow all actions and reusable workflows
- **Workflow permissions:** Read and write permissions
- **Artifact and log retention:** 90 days

## Complete Settings Checklist

Use this checklist when setting up the repository:

### Basic Information
- [ ] Repository description added
- [ ] Website URL set to https://lightspeedup.com
- [ ] Topics/tags added (at least 10)
- [ ] Social preview image uploaded (or using default)

### Features
- [ ] Issues enabled
- [ ] Discussions enabled
- [ ] Projects enabled (optional)
- [ ] Wiki enabled (optional)
- [ ] Preserve repository enabled

### Pull Request Settings
- [ ] Merge strategies configured
- [ ] Auto-delete head branches enabled
- [ ] Branch suggestions enabled

### Security
- [ ] Dependency graph enabled
- [ ] Dependabot alerts enabled
- [ ] Security updates enabled
- [ ] Private vulnerability reporting enabled

### Documentation
- [ ] README.md is comprehensive ✅
- [ ] CONTRIBUTING.md present ✅
- [ ] CODE_OF_CONDUCT.md present ✅
- [ ] SECURITY.md present ✅
- [ ] LICENSE present ✅
- [ ] CHANGELOG.md present ✅

### Community
- [ ] Issue templates configured ✅
- [ ] Pull request template configured ✅
- [ ] Discussion categories set up (if enabled)
- [ ] Community profile complete (check at /community)

## Step-by-Step: Complete Repository Setup

### Step 1: Update About Section

1. Go to https://github.com/MatoTeziTanka/SethFlix-Plex
2. Look for the "About" section on the right side
3. Click the ⚙️ **gear icon**
4. Fill in:
   - **Description:** (copy from above)
   - **Website:** https://lightspeedup.com
   - **Topics:** (add all recommended topics)
5. Click **Save changes**

### Step 2: Enable Discussions

1. Go to **Settings** → **General**
2. Scroll to **Features**
3. Check ✅ **Discussions**
4. Click **Set up discussions**
5. GitHub will create initial discussion categories

### Step 3: Verify Issue Templates

1. Go to **Issues** → **New issue**
2. Verify you see:
   - Bug Report
   - Feature Request
   - Documentation Issue
   - Contact links
3. If not visible, check `.github/ISSUE_TEMPLATE/` files are committed ✅

### Step 4: Configure Security

1. Go to **Settings** → **Security & analysis**
2. Enable all recommended features:
   - Dependency graph
   - Dependabot alerts
   - Dependabot security updates
3. Go to **Settings** → **Security** → **Code security and analysis**
4. Enable **Private vulnerability reporting**

### Step 5: Check Community Profile

1. Go to **Insights** → **Community**
2. Verify all items are green checkmarks:
   - ✅ Description
   - ✅ README
   - ✅ Code of conduct
   - ✅ Contributing
   - ✅ License
   - ✅ Issue templates
   - ✅ Pull request template
3. If any are missing, add them

### Step 6: Create Repository Labels

Navigate to: **Issues** → **Labels**

Add these custom labels (in addition to defaults):

| Label | Color | Description |
|-------|-------|-------------|
| `documentation` | `0075ca` | Documentation improvements |
| `hardware` | `d4c5f9` | Hardware-related issues |
| `proxmox` | `fbca04` | Proxmox-specific topics |
| `plex` | `cc317c` | Plex-specific topics |
| `zfs` | `1d76db` | ZFS/storage issues |
| `security` | `d73a4a` | Security-related |
| `question` | `d876e3` | General questions |
| `help wanted` | `008672` | Community help needed |
| `good first issue` | `7057ff` | Good for newcomers |

## Optional: GitHub Repository Insights

Configure repository insights to track:

### Traffic
Monitor:
- Views over time
- Clone statistics
- Popular content

### Community
Track:
- Issues opened/closed
- Pull requests
- Contributors

### Pulse
Weekly snapshot of repository activity

## Post-Setup Verification

After completing all settings:

1. **Visit your repository as a visitor** (incognito/logged out)
2. Verify:
   - Description shows correctly
   - Topics are visible and clickable
   - README renders properly
   - Issues can be created with templates
   - All links work
   - Social preview looks good (share on social media to test)

3. **Check Community Profile Score**
   - Go to `/community`
   - Should show 100% complete
   - All recommended files present

4. **Test Contribution Flow**
   - Try creating an issue with each template
   - Verify contact links work
   - Check pull request template renders

## Maintenance

### Weekly
- Review new issues and discussions
- Check for Dependabot alerts
- Monitor traffic/engagement

### Monthly
- Update CHANGELOG.md
- Review and close stale issues
- Check for outdated documentation

### Quarterly
- Review and update repository description/topics
- Refresh social preview image if needed
- Audit security settings

## Current Status

As of November 4, 2025:

✅ **Complete:**
- All documentation files
- Issue templates
- Pull request template
- Community health files
- Repository structure

⏳ **Needs Action (Web Interface Only):**
- [ ] Set repository description
- [ ] Add website URL
- [ ] Add topics/tags
- [ ] Enable Discussions
- [ ] Configure security features
- [ ] Add custom labels
- [ ] Upload social preview image (optional)

## Quick Copy-Paste Values

For convenience when setting up:

**Description:**
```
Self-hosted media streaming infrastructure built on Dell PowerEdge R730, Proxmox VE, and Plex Media Server. Complete documentation for enterprise-grade home media server setup with ZFS storage.
```

**Website:**
```
https://lightspeedup.com
```

**Topics (comma-separated for easy paste):**
```
plex, proxmox, self-hosted, media-server, home-lab, zfs, dell-poweredge, infrastructure, virtualization, homelab, plex-media-server, streaming, nas, storage, r730, documentation
```

---

## Need Help?

If you need assistance with any GitHub settings:
- Email: support@lightspeedup.com
- GitHub Docs: https://docs.github.com/

---

**Once complete, this repository will be a professional, community-ready open source project!** 🎉





