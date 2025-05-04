# Zenon Network Orchestrator Ansible Deployment

Version: 0.0.1 (Development)

This Ansible playbook automates the deployment of the Zenon Orchestrator on a pillar node. Ansible is an automation tool that runs on your local computer and connects to remote servers (in this case, your pillar node) to perform configuration and deployment tasks. It uses SSH to securely connect to the remote server and execute commands, making it a safe and efficient way to manage your orchestrator deployment.

## How Ansible Works

1. **Local Execution**: Ansible runs on your local computer, not on the pillar node
2. **SSH Connection**: It connects to your pillar node via SSH to perform tasks
3. **Idempotent Operations**: Tasks are designed to be safe to run multiple times
4. **No Agent Required**: No software needs to be installed on the pillar node besides SSH
5. **Configuration as Code**: All deployment steps are defined in YAML files


## Prerequisites

- Ansible installed on your **LOCAL MACHINE** - not the pillar
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

Intall Ansible on your **LOCAL MACHINE** not the Pillar.

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

## Step by Step Configuration

### Step 1: Clone the Repository

1. Clone the repository:
   ```bash
   git clone https://github.com/hypercore-one/orchestrator-ansible-deployment.git
   cd orchestrator-ansible-deployment
   ```

### Step 2: Configure inventory.ini

1. Copy the example inventory file:
   ```bash
   cp inventory.ini.example inventory.ini
   ```

2. Edit the inventory file:
   ```bash
   nano inventory.ini
   ```

3. Update the following in the file:
   ```ini
   # Pillar deployment (orchestrator runs on the pillar)
   [orchestrators]
   pillar ansible_host=IP_ADDRESS  # Replace IP_ADDRESS with your pillar's IP
   ```
   - Only change the `IP_ADDRESS` value
   - Other settings are pre-configured for password authentication
   - The file already includes:
     - Root user access
     - Sudo privilege escalation
     - SSH settings for password authentication

### Step 3: Configure vars/main.yml

1. Copy the example variables file:
   ```bash
   cp vars/main.yml.example vars/main.yml
   ```

2. Edit the variables file:
   ```bash
   nano vars/main.yml
   ```

3. Update the following sections:

   a. **Network Configuration**:
   ```yaml
   eth_node_ws_url: "wss://your-eth-node-url"        # Replace with your ETH node URL
   bnb_chain_node_ws_url: "wss://your-bnb-node-url"  # Replace with your BSC node URL
   supernova_node_ws_url: "wss://your-supernova-node-url"  # Replace with your Supernova node URL
   zenon_node_ws_url: "ws://127.0.0.1:35998"         # Keep as is if running locally
   zenon_node_http_url: "http://127.0.0.1:35997"     # Keep as is if running locally
   ```

   b. **Producer Configuration**:
   ```yaml
   producer_key_passphrase: "your-passphrase-here"    # Replace with your passphrase
   producer_address: "your-producer-address-here"     # Replace with your address (must have 120 QSR)
   ```

   c. **Optional Settings** (only change if different from defaults):
   ```yaml
   min_cpu_cores: 2      # Minimum CPU cores required
   min_ram_gb: 4         # Minimum RAM required in GB
   min_disk_gb: 40       # Minimum disk space required in GB
   orchestrator_version: "v0.0.9a-alphanet"  # Only change if using a different version
   ```

Note: Both `inventory.ini` and `vars/main.yml` contain sensitive information and are excluded from git by default.

### Step 4: Deploy the Orchestrator

1. Run the playbook:
   ```bash
   ansible-playbook -i inventory.ini main.yml --ask-pass
   ```
   - You will be prompted for the root password of your pillar node
   - The playbook will perform all necessary checks and configurations
   - Progress will be displayed in real-time

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