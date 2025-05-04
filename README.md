# Zenon Network Orchestrator Ansible Deployment

Version: 0.0.1 (Development)

This Ansible playbook automates the deployment of the Zenon Orchestrator on a pillar node. Ansible is an automation tool that runs on your local computer and connects to remote servers (in this case, your pillar node) to perform configuration and deployment tasks. It uses SSH to securely connect to the remote server and execute commands, making it a safe and efficient way to manage your orchestrator deployment.

## How Ansible Works

1. **Local Execution**: Ansible runs on your local computer, not on the pillar node
2. **SSH Connection**: It connects to your pillar node via SSH to perform tasks
3. **Idempotent Operations**: Tasks are designed to be safe to run multiple times
4. **No Agent Required**: No software needs to be installed on the pillar node besides SSH
5. **Configuration as Code**: All deployment steps are defined in YAML files

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
- Public Nodes for ETH, BSC and Supernova
- 120 QSR fused to the producer address.  The installation will abort if sufficent QSR is not fused.

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

2. Create and configure your inventory and variables files:
   ```bash
   # Create inventory file
   cp inventory.ini.example inventory.ini
   
   # Create variables file
   cp vars/main.yml.example vars/main.yml
   ```
   
   Edit `inventory.ini` and `vars/main.yml` with your specific configuration:
   - Your pillar's IP address
   - Root password
   - Node URLs (ETH, BSC, Zenon) - must start with ws:// or wss://
   - Producer key passphrase
   - System requirements (if different from defaults)
   - Bootstrap configuration (if different from defaults)

   Note: Both `inventory.ini` and `vars/main.yml` contain sensitive information and are excluded from git by default.

3. Run the playbook:
   ```bash
   ansible-playbook -i inventory.ini main.ymlc --ask-pass
   ```

## Configuration

Configuration is done through two main files:

1. `inventory.ini`: Contains pillar connection details and basic configuration
2. `vars/main.yml`: Contains detailed configuration including:
   - Orchestrator installation paths and version
   - Network node URLs (ws:// or wss://, with optional port)
   - Producer configuration
   - System requirements
   - Bootstrap configuration

Example files (`inventory.ini.example` and `vars/main.yml.example`) are provided as templates. Copy these to create your actual configuration files:

```bash
cp inventory.ini.example inventory.ini
cp vars/main.yml.example vars/main.yml
```

Then edit the new files with your specific configuration. These files contain sensitive information and are excluded from git by default.

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
   - Tests connectivity to Zenon node
   - Verifies UFW configuration (if enabled)

3. Orchestrator Health:
   - Service status verification
   - P2P port availability (port 55055)
   - API port availability (port 55000)
   - Log analysis for errors

## Security Notes

- The `inventory.ini` and `vars/main.yml` files contain sensitive information. Keep them secure and never commit them to version control.
- These files are automatically excluded from git via `.gitignore`
- Consider using Ansible Vault to encrypt sensitive variables in your configuration files
- The producer key file is copied with restricted permissions (0600) to ensure only root can access it
- Existing files are preserved during updates, with backups created before any modifications
- UFW rules are only added if the firewall is already active, maintaining your security preferences

## Updating

The playbook is designed to handle updates safely:
- Existing configuration is preserved
- Backups are created before any file modifications
- The service is properly stopped and started during updates
- The latest version of the orchestrator binary is always downloaded
- Health checks are performed after updates
- UFW rules are preserved during updates

## Changelog

### 0.0.1 (2025-05-04)
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