# Contributing to SethFlix-Plex

First off, thank you for considering contributing to SethFlix-Plex! This is an open-source project documenting a self-hosted media server infrastructure, and we welcome contributions from the community.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Getting Started](#getting-started)
- [Contribution Guidelines](#contribution-guidelines)
- [Style Guidelines](#style-guidelines)
- [Commit Messages](#commit-messages)
- [Pull Request Process](#pull-request-process)

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Please report unacceptable behavior to support@lightspeedup.com.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check the existing issues to avoid duplicates. When you create a bug report, include as many details as possible:

- **Use a clear and descriptive title**
- **Describe the exact steps to reproduce the problem**
- **Provide specific examples** (configuration files, commands, etc.)
- **Describe the behavior you observed and what you expected**
- **Include system information** (Proxmox version, ZFS version, Plex version, etc.)

### Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

- **Use a clear and descriptive title**
- **Provide a detailed description** of the suggested enhancement
- **Explain why this enhancement would be useful** to most users
- **List any similar features** in other projects if applicable

### Documentation Improvements

Documentation is critical! We welcome:

- Corrections to typos or grammatical errors
- Additional examples and use cases
- Clarifications to confusing sections
- New guides for common tasks
- Translations (if applicable in the future)

### Configuration Examples

Share your working configurations:

- Proxmox VM configurations
- ZFS pool setups
- Plex optimization settings
- Network configurations
- Backup scripts

## Getting Started

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/SethFlix-Plex.git
   cd SethFlix-Plex
   ```
3. **Create a branch** for your changes:
   ```bash
   git checkout -b feature/your-feature-name
   ```
4. **Make your changes** and test them
5. **Commit your changes** with clear commit messages
6. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```
7. **Submit a pull request** through GitHub

## Contribution Guidelines

### Documentation Standards

- Use clear, concise language
- Include code examples where applicable
- Use proper markdown formatting
- Test all commands and configurations before submitting
- Include links to official documentation when referencing external tools

### Code/Script Standards

If contributing scripts or automation:

- Include comments explaining complex logic
- Follow bash/Python best practices
- Include error handling
- Test on a similar environment
- Document any dependencies

### File Organization

- Keep README.md as the main entry point
- Use the `docs/` directory for detailed guides
- Place scripts in appropriately named directories
- Use meaningful file and directory names

## Style Guidelines

### Markdown Style

- Use ATX-style headers (`#` not underlines)
- Use fenced code blocks with language identifiers
- Use tables for structured data
- Include a table of contents for long documents
- Use relative links for internal documentation

### Command Examples

Format command examples clearly:

```bash
# Good: Include comments and expected output
zpool status Storage_HDD
# Expected: Shows pool health status

# Bad: No context
zpool status
```

### Configuration Examples

When sharing configurations:

- Remove sensitive information (IPs, passwords, tokens)
- Add comments explaining each section
- Include the file path where it should be placed
- Note the required software versions

## Commit Messages

Write clear, meaningful commit messages:

### Format

```
[Type] Brief description (50 chars or less)

More detailed explanatory text, if necessary. Wrap at 72 characters.
Explain the problem this commit solves and why this approach was taken.

- Bullet points are okay
- Use present tense ("Add feature" not "Added feature")
- Reference issues and pull requests liberally
```

### Types

- **[Docs]** - Documentation changes
- **[Fix]** - Bug fixes
- **[Feature]** - New features or enhancements
- **[Config]** - Configuration updates
- **[Script]** - New or updated scripts
- **[Refactor]** - Code/documentation reorganization
- **[Test]** - Test additions or updates

### Examples

Good:
```
[Docs] Add troubleshooting guide for ZFS pool degradation

Includes step-by-step instructions for identifying and replacing
failed drives, with real-world examples and expected output.

Fixes #42
```

Bad:
```
Updated readme
```

## Pull Request Process

1. **Update documentation** - If your changes require it, update the README.md or other relevant docs
2. **Follow the style guidelines** - Ensure your contribution matches the project's style
3. **Test your changes** - Verify commands work and documentation is clear
4. **Fill out the PR template** - Provide all requested information
5. **Link related issues** - Use "Fixes #123" or "Relates to #456"
6. **Be responsive** - Address review comments promptly
7. **One feature per PR** - Keep pull requests focused on a single improvement

### PR Title Format

Use the same format as commit messages:
```
[Type] Brief description of changes
```

### What to Expect

- Initial review within 1-3 days
- Constructive feedback on improvements
- Possible requests for changes or additional information
- Merge once approved by a maintainer

## Recognition

Contributors will be:
- Listed in the repository's contributors page
- Mentioned in the changelog for significant contributions
- Credited in the acknowledgments section

## Questions?

- **General questions**: Open a GitHub Discussion
- **Bug reports**: Create a GitHub Issue
- **Security concerns**: Email support@lightspeedup.com
- **Private inquiries**: Email support@lightspeedup.com

## License

By contributing to SethFlix-Plex, you agree that your contributions will be licensed under the [Apache License 2.0](LICENSE).

---

**Thank you for contributing to SethFlix-Plex!** 🎉

Your efforts help make self-hosted media infrastructure more accessible and better documented for everyone.





