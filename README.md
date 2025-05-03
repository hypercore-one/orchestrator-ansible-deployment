# Zenon Network Orchestrator Ansible Deployment

Version: 0.0.1 (Development)

This Ansible playbook automates the deployment of the Zenon Orchestrator on a pillar node.

## Versioning

This project follows [Semantic Versioning](https://semver.org/):
- Major version (0.x.x): Initial development, unstable API
- Minor version (x.1.x): Backwards-compatible functionality additions
- Patch version (x.x.1): Backwards-compatible bug fixes

Current version: 0.0.1 (Development)
- Initial development release
- Basic orchestrator deployment functionality
- System requirement checks
- Network connectivity validation
- Health monitoring
- Secure configuration handling
- UFW port configuration (if enabled)

Note: Version 1.0.0 will be released when the playbook is considered stable and production-ready.

## Prerequisites

- Ansible installed on your local machine
- SSH access to your pillar node
- Root access to your pillar node
- Existing Zenon wallet with producer key file at `/root/.znn/wallet/producer`
- System requirements:
  - x86_64 architecture
  - Minimum 2 CPU cores
  - Minimum 4GB RAM
  - Minimum 20GB free disk space
- Network requirements:
  - Port 55055 (P2P) must be accessible
  - Port 55000 (API) must be accessible
  - If UFW is enabled, these ports will be automatically configured

## Installing Ansible

### macOS
```bash
# Using Homebrew
brew install ansible

# Verify installation
ansible --version
```

### Linux (Ubuntu/Debian)
```bash
# Update package lists
sudo apt update

# Install software-properties-common
sudo apt install software-properties-common

# Add Ansible repository
sudo apt-add-repository --yes --update ppa:ansible/ansible

# Install Ansible
sudo apt install ansible

# Verify installation
ansible --version
```

### Windows
1. Install Windows Subsystem for Linux (WSL):
   ```powershell
   # Open PowerShell as Administrator and run:
   wsl --install
   ```
2. After WSL is installed, open Ubuntu from the Start menu
3. Follow the Linux (Ubuntu/Debian) instructions above

## Quick Start

1. Clone this repository:
   ```bash
   git clone https://github.com/hypercore-one/orchestrator-ansible-deployment.git
   cd orchestrator-ansible-deployment
   ```

2. Create and configure your inventory file:
   ```bash
   cp inventory.ini.example inventory.ini
   ```
   Edit `inventory.ini` and update:
   - Your pillar's IP address
   - Root password
   - Node URLs (ETH, BSC, Zenon) - must start with ws:// or wss://
   - Producer key passphrase
   - System requirements (if different from defaults)
   - Bootstrap configuration (if different from defaults)

3. Run the playbook:
   ```bash
   ansible-playbook -i inventory.ini main.yml
   ```

## Configuration

All configuration is done through the `inventory.ini` file. The file includes:

- Pillar connection details (IP, credentials)
- Orchestrator installation paths and version
- Network node URLs (ws:// or wss://, with optional port)
- Producer configuration
- System requirements
- Bootstrap configuration

## File Structure and Permissions

The playbook sets up the following structure with appropriate permissions:

- `/root/.orchestrator/` (0755): Main configuration directory
  - `producer` (0600): Producer key file (copied from `/root/.znn/wallet/producer`)
  - `config.json` (0644): Orchestrator configuration
- `/usr/local/bin/orchestrator` (0755): Orchestrator binary

## Network Configuration

### Required Ports
- Port 55055: P2P communication
- Port 55000: API access

### UFW Configuration
If UFW (Uncomplicated Firewall) is installed and enabled on your system:
- The playbook will automatically configure UFW to allow inbound traffic on required ports
- UFW will NOT be enabled if it's not already active
- No changes will be made to UFW if it's not installed
- Port configuration will be skipped if UFW is not active

## Health Checks

The playbook performs several health checks during deployment:

1. System Requirements:
   - Architecture verification (x86_64)
   - CPU core count
   - Available RAM
   - Free disk space

2. Network Connectivity:
   - Tests connectivity to ETH node
   - Tests connectivity to BSC node
   - Tests connectivity to Zenon node
   - Verifies UFW configuration (if enabled)

3. Orchestrator Health:
   - Service status verification
   - P2P port availability (port 55055)
   - API port availability (port 55000)
   - Health endpoint check
   - Log analysis for errors

## Security Notes

- The `inventory.ini` file contains sensitive information. Keep it secure and never commit it to version control.
- Consider using Ansible Vault to encrypt sensitive variables in your inventory file.
- The producer key file is copied with restricted permissions (0600) to ensure only root can access it.
- Existing files are preserved during updates, with backups created before any modifications.
- UFW rules are only added if the firewall is already active, maintaining your security preferences.

## Updating

The playbook is designed to handle updates safely:
- Existing configuration is preserved
- Backups are created before any file modifications
- The service is properly stopped and started during updates
- The latest version of the orchestrator binary is always downloaded
- Health checks are performed after updates
- UFW rules are preserved during updates

## Changelog

### 0.0.1 (2024-03-21)
- Initial development release
- Basic orchestrator deployment functionality
- System requirement checks
- Network connectivity validation
- Health monitoring
- Secure configuration handling
- Support for ws:// and wss:// node URLs
- Optional port numbers in node URLs
- UFW port configuration (if enabled)

## License

MIT License 