# Zenon Network Orchestrator Ansible Deployment

This Ansible playbook automates the deployment of the Zenon Orchestrator on a pillar node.

## Prerequisites

- Ansible installed on your local machine
- SSH access to your pillar node
- Root access to your pillar node
- Existing Zenon wallet with producer key file at `/root/.znn/wallet/producer`

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

### Linux (RHEL/CentOS/Fedora)
```bash
# Install EPEL repository (if not already installed)
sudo dnf install epel-release

# Install Ansible
sudo dnf install ansible

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
   - Node URLs (ETH, BSC, Zenon)
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
- Network node URLs
- Producer configuration
- System requirements
- Bootstrap configuration

## File Structure and Permissions

The playbook sets up the following structure with appropriate permissions:

- `/root/.orchestrator/` (0755): Main configuration directory
  - `producer` (0600): Producer key file (copied from `/root/.znn/wallet/producer`)
  - `config.json` (0644): Orchestrator configuration
- `/usr/local/bin/orchestrator` (0755): Orchestrator binary

## Security Notes

- The `inventory.ini` file contains sensitive information. Keep it secure and never commit it to version control.
- Consider using Ansible Vault to encrypt sensitive variables in your inventory file.
- The producer key file is copied with restricted permissions (0600) to ensure only root can access it.
- Existing files are preserved during updates, with backups created before any modifications.

## Updating

The playbook is designed to handle updates safely:
- Existing configuration is preserved
- Backups are created before any file modifications
- The service is properly stopped and started during updates
- The latest version of the orchestrator binary is always downloaded

## License

MIT License 