# Comprehensive Ubuntu Server Setup for N8N Agentic Workflows

This guide provides detailed instructions for setting up, securing, and configuring an Ubuntu server with Nginx for web hosting, with advanced configurations for running production-grade N8N agentic workflows.

## Table of Contents

1. [Initial System Setup](#1-initial-system-setup) - [↑](#table-of-contents)
2. [User Management and SSH Security](#2-user-management-and-ssh-security) - [↑](#table-of-contents)
3. [Firewall Configuration](#3-firewall-configuration) - [↑](#table-of-contents)
4. [System Hardening](#4-system-hardening) - [↑](#table-of-contents)
5. [Nginx Web Server Setup](#5-nginx-web-server-setup) - [↑](#table-of-contents)
6. [SSL/TLS Configuration](#6-ssltls-configuration) - [↑](#table-of-contents)
7. [Monitoring and Maintenance](#7-monitoring-and-maintenance) - [↑](#table-of-contents)
8. [Backup Strategy](#8-backup-strategy) - [↑](#table-of-contents)
9. [WSL-Specific Considerations](#9-wsl-specific-considerations) - [↑](#table-of-contents)
   - [Installing and Setting Up WSL Using System PowerShell](#installing-and-setting-up-wsl-using-system-powershell)
   - [Service Management in WSL](#service-management-in-wsl)
   - [Configuring WSL to Start Automatically with Windows](#configuring-wsl-to-start-automatically-with-windows)
   - [Automatic Service Startup Within WSL](#automatic-service-startup-within-wsl)
   - [Setting Up Llama for External Access](#setting-up-llama-for-external-access)
     - [Exposing Llama API Externally](#exposing-llama-api-externally)
       - [Our Chosen Solution: Cloudflare Tunnel for Llama API](#our-chosen-solution-cloudflare-tunnel-for-llama-api)
   - [Port Forwarding from Windows to WSL](#port-forwarding-from-windows-to-wsl)
   - [Handling WSL IP Changes](#handling-wsl-ip-changes)
   - [Accessing Your WSL Server](#accessing-your-wsl-server)
     - [Local Access](#local-access)
     - [Setting Up Remote Access from Anywhere](#setting-up-remote-access-from-anywhere)
       - [Our Chosen Solution: Cloudflare Tunnel](#our-chosen-solution-cloudflare-tunnel-for-always-on-access)
       - [Alternative Options](#alternative-options)
   - [Troubleshooting Common WSL Issues](#troubleshooting-common-wsl-issues)
   - [Accessing WSL from Different IDEs](#accessing-wsl-from-different-ides)
10. [Advanced: Containerization with Docker](#10-advanced-containerization-with-docker) - [↑](#table-of-contents)
11. [Advanced: Supabase Setup](#11-advanced-supabase-setup) - [↑](#table-of-contents)
12. [Advanced: N8N Configuration](#12-advanced-n8n-configuration) - [↑](#table-of-contents)
13. [Advanced: Security Hardening](#13-advanced-security-hardening) - [↑](#table-of-contents)
14. [Advanced: Observability Stack](#14-advanced-observability-stack) - [↑](#table-of-contents)
15. [Advanced: Workflow Management](#15-advanced-workflow-management) - [↑](#table-of-contents)
16. [Advanced: High Availability](#16-advanced-high-availability) - [↑](#table-of-contents)

## 1. Initial System Setup

### Update and Upgrade System
```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade -y

# Install essential packages
sudo apt install -y build-essential curl file git
```

### Set Timezone
```bash
# Check current timezone
timedatectl

# List available timezones
timedatectl list-timezones

# Set your timezone (replace "America/New_York" with your timezone)
sudo timedatectl set-timezone America/New_York
```

[↑ Return to Top](#table-of-contents)

## 2. User Management and SSH Security
   - [Create a New Admin User](#create-a-new-admin-user)
   - [SSH Configuration](#ssh-configuration)
     - [Step 1: Initial SSH Security Configuration](#step-1-initial-ssh-security-configuration)
     - [Step 2: Set Up SSH Keys](#step-2-set-up-ssh-keys-from-your-local-machine)
     - [Step 3: Disable Password Authentication](#step-3-disable-password-authentication-final-security-step)

### Create a New Admin User

Creating a dedicated admin user (instead of using the default root account) is a critical security practice for several important reasons:

1. **Security Principle**: Following the principle of least privilege - root access should only be used when absolutely necessary
2. **Audit Trail**: Maintaining clear logs of which user performed administrative actions
3. **Attack Surface Reduction**: Limiting potential attack vectors by disabling direct root login
4. **Accountability**: Ensuring individual accountability in multi-admin environments
5. **Protection Against Mistakes**: Adding an extra confirmation step (sudo) before executing potentially dangerous commands

```bash
# Create a new dedicated admin user with a strong password
# Replace 'newadmin' with your preferred username
sudo adduser newadmin

# Add the new user to the sudo group to grant administrative privileges
# This allows the user to execute commands with root privileges using sudo
sudo usermod -aG sudo newadmin

# Switch to the new admin user to verify the setup
# You should be able to execute administrative commands using sudo
su - newadmin
```

### SSH Configuration

Securing SSH is essential as it's often a primary target for attackers. This process should be done in a specific sequence to avoid locking yourself out of the server.

#### Step 1: Initial SSH Security Configuration

```bash
# Open the SSH server configuration file for editing
sudo nano /etc/ssh/sshd_config
```

Make these initial security changes and understand their purpose:

- **Change default port**: `Port 2222`
  - *Why*: Reduces automated attacks targeting the default SSH port (22)
  - *Note*: Choose any non-standard port between 1024-65535 that isn't used by other services

- **Disable root login**: `PermitRootLogin no`
  - *Why*: Prevents direct root access, forcing attackers to compromise a regular account first
  - *Note*: If you see `PermitRootLogin prohibit-password`, change it to `no` for better security

- **Limit user login**: `AllowUsers yourusername`
  - *Why*: Explicitly specifies which users can connect via SSH, blocking all others
  - *Note*: Replace 'yourusername' with your actual username
  - *Note*: Add this line if it doesn't exist, typically after the PermitRootLogin line

- **Enable key authentication**: `PubkeyAuthentication yes`
  - *Why*: SSH keys are significantly more secure than passwords
  - *Note*: This must be enabled before proceeding to key setup

- **Keep password authentication enabled for now**: `PasswordAuthentication yes`
  - *Why*: Ensures you don't lock yourself out before setting up SSH keys
  - *Note*: We'll disable this in Step 3 after confirming key authentication works

- **Set login grace time**: `LoginGraceTime 30`
  - *Why*: Limits how long the SSH server waits for authentication, reducing DoS vulnerability
  - *Note*: 30 seconds is usually sufficient for legitimate connections

- **Set max auth tries**: `MaxAuthTries 3`
  - *Why*: Limits brute force attempts per connection
  - *Note*: Legitimate users rarely need more than 2-3 attempts

```bash
# Apply the initial configuration changes
sudo service ssh restart
# or if using systemd:
# sudo systemctl restart sshd
```

#### Step 2: Set Up SSH Keys (from your local machine)

SSH key authentication is vastly more secure than passwords because:
- Keys are significantly longer and more complex than typical passwords
- Private keys never travel over the network
- Each service/device can have a unique key for better access control
- Compromised servers can't steal authentication credentials

##### Option A: Setting Up SSH Keys on the Same WSL Instance

If you're setting up SSH on the same WSL instance you're working in:

```bash
# Generate a new SSH key pair
ssh-keygen -t ed25519 -C "jerryagenyi@gmail.com"

# You'll be prompted to:
# 1. Specify where to save the key (accept default or specify custom location)
# 2. Enter a passphrase (highly recommended for additional security)

# If you need to access the key content from Windows:
cat ~/.ssh/id_ed25519.pub > /mnt/c/Users/Username/Desktop/my_ssh_key.pub

# Set up the authorized_keys file directly (no need for ssh-copy-id)
mkdir -p ~/.ssh
chmod 700 ~/.ssh
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# Test SSH connection locally
ssh -p 2222 yourusername@localhost
```

##### Option B: Setting Up SSH Keys from Another Machine

If you're setting up SSH keys on a different machine to access your WSL server:

```bash
# On your client machine, generate a new SSH key pair
ssh-keygen -t ed25519 -C "jerryagenyi@gmail.com"

# Copy your public key to the server
# Replace 'yourusername' with your admin username
# Replace 'your_server_ip' with your server's IP address or hostname
# The -p flag specifies the custom SSH port you configured
ssh-copy-id -i ~/.ssh/id_ed25519.pub yourusername@your_server_ip -p 2222

# If ssh-copy-id isn't available (e.g., on Windows), you can manually:
# 1. Display your public key: cat ~/.ssh/id_ed25519.pub
# 2. Copy the output
# 3. On the server: mkdir -p ~/.ssh && nano ~/.ssh/authorized_keys
# 4. Paste your key and save

# IMPORTANT: Test key-based login in a new terminal window BEFORE proceeding to Step 3
# This ensures you don't lock yourself out
ssh -p 2222 yourusername@your_server_ip
```

#### Step 3: Disable Password Authentication (Final Security Step)

Only proceed with this step after confirming that key-based authentication is working properly.

```bash
# Edit SSH config again
sudo nano /etc/ssh/sshd_config

# Find the PasswordAuthentication line and change it to:
PasswordAuthentication no

# Apply the final configuration
sudo service ssh restart
# or if using systemd:
# sudo systemctl restart sshd

# Test that you can still log in with your SSH key
# If you can't log in, you may need to revert this change
```

[↑ Return to Top](#table-of-contents)

## 3. Firewall Configuration

### Install and Configure UFW (Uncomplicated Firewall)
```bash
# Install UFW
sudo apt install -y ufw

# Set default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (use your custom port if changed)
sudo ufw allow 2222/tcp

# Allow HTTP and HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Enable firewall
sudo ufw enable

# Check status
sudo ufw status verbose
```

[↑ Return to Top](#table-of-contents)

## 4. System Hardening

### Secure Shared Memory
```bash
# Edit fstab
sudo nano /etc/fstab
```

Add this line:
```
tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0
```

### Disable Unused Services
```bash
# List all running services
systemctl list-units --type=service --state=running

# Disable unnecessary services (example)
sudo systemctl disable bluetooth.service
sudo systemctl stop bluetooth.service
```

### Configure Automatic Security Updates
```bash
# Install unattended-upgrades
sudo apt install -y unattended-upgrades apt-listchanges

# Configure
sudo dpkg-reconfigure -plow unattended-upgrades
```

### Harden Network Parameters
```bash
# Edit sysctl.conf
sudo nano /etc/sysctl.conf
```

Add these lines:
```
# IP Spoofing protection
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Ignore ICMP broadcast requests
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0

# Ignore send redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# Block SYN attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5
```

Apply changes:
```bash
sudo sysctl -p
```

[↑ Return to Top](#table-of-contents)

## 5. Nginx Web Server Setup

### Install Nginx
```bash
# Install Nginx
sudo apt install -y nginx

# Start and enable Nginx (WSL-specific)
sudo service nginx start

# For WSL, to start Nginx automatically when WSL starts, add to ~/.bashrc:
echo "# Start Nginx when WSL starts" >> ~/.bashrc
echo "if service nginx status 2>&1 | grep -q 'not running'; then" >> ~/.bashrc
echo "  sudo service nginx start" >> ~/.bashrc
echo "fi" >> ~/.bashrc
```

### Create Web Root Directory
```bash
# Create web root directory for jerryagenyi.xyz
sudo mkdir -p /var/www/jerryagenyi.xyz/html

# Set permissions
sudo chown -R $USER:$USER /var/www/jerryagenyi.xyz/html
sudo chmod -R 755 /var/www/jerryagenyi.xyz
```

### Create a Basic Website
```bash
# Create a sample index.html
sudo nano /var/www/jerryagenyi.xyz/html/index.html
```

Add basic HTML:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to jerryagenyi.xyz!</title>
</head>
<body>
    <h1>Success! Your Nginx server is running.</h1>
    <p>This is a test page for jerryagenyi.xyz.</p>
</body>
</html>
```

### Configure Nginx Server Block
```bash
# Create server block configuration
sudo nano /etc/nginx/sites-available/jerryagenyi.xyz
```

Add this configuration:
```nginx
server {
    listen 80;
    listen [::]:80;

    root /var/www/jerryagenyi.xyz/html;
    index index.html index.htm index.nginx-debian.html;

    server_name jerryagenyi.xyz www.jerryagenyi.xyz;

    # Add Cloudflare headers
    real_ip_header CF-Connecting-IP;

    # Log settings
    access_log /var/log/nginx/jerryagenyi.xyz.access.log;
    error_log /var/log/nginx/jerryagenyi.xyz.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Enable the site:
```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/jerryagenyi.xyz /etc/nginx/sites-enabled/

# Remove default site if desired
sudo rm /etc/nginx/sites-enabled/default

# Test configuration
sudo nginx -t

# Restart Nginx (WSL-specific)
sudo service nginx restart
```

### Configure Nginx for Direct Access (for N8N on Google VM)

If you need to allow direct access from your N8N installation on Google VM to bypass Cloudflare for reduced latency:

```bash
# Create a separate server block for direct access
sudo nano /etc/nginx/sites-available/direct.jerryagenyi.xyz
```

Add this configuration:
```nginx
server {
    listen 8080;  # Use a different port for direct access

    root /var/www/jerryagenyi.xyz/html;
    index index.html index.htm index.nginx-debian.html;

    server_name direct.jerryagenyi.xyz;

    # Optional: IP restriction to only allow your Google VM
    # Replace with your Google VM's IP address
    allow 35.X.X.X;  # Your Google VM IP
    deny all;

    location / {
        try_files $uri $uri/ =404;

        # Add any specific API endpoints needed for N8N
        # location /api/ {
        #     proxy_pass http://localhost:3000;
        #     proxy_set_header Host $host;
        #     proxy_set_header X-Real-IP $remote_addr;
        # }
    }
}
```

Enable the direct access site:
```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/direct.jerryagenyi.xyz /etc/nginx/sites-enabled/

# Update firewall to allow the direct access port
sudo ufw allow 8080/tcp

# Test configuration
sudo nginx -t

# Restart Nginx (WSL-specific)
sudo service nginx restart
```

[↑ Return to Top](#table-of-contents)

## 6. SSL/TLS Configuration

### Option 1: Cloudflare SSL (Recommended for your setup)

When using Cloudflare as your DNS provider with SSL, you have several options:

#### Flexible SSL (Cloudflare to Visitor is HTTPS, Cloudflare to Server is HTTP)
- Easiest to set up but less secure
- No certificate needed on your server
- Configure Nginx to listen on port 80 only

#### Full SSL (HTTPS on both connections, self-signed cert OK on server)
- More secure than Flexible
- Requires a self-signed certificate on your server
- Cloudflare validates the certificate but doesn't verify its authenticity

#### Full (Strict) SSL (HTTPS with trusted cert on both connections)
- Most secure option
- Requires a valid certificate on your server (can use Cloudflare Origin Certificate)

### Setting up Cloudflare Origin Certificate

```bash
# Create directory for certificates
sudo mkdir -p /etc/nginx/ssl/jerryagenyi.xyz

# Install the certificates (you'll need to get these from Cloudflare dashboard)
# Navigate to SSL/TLS > Origin Server in Cloudflare dashboard and create a certificate
sudo nano /etc/nginx/ssl/jerryagenyi.xyz/origin.pem
# Paste the certificate content

sudo nano /etc/nginx/ssl/jerryagenyi.xyz/origin.key
# Paste the private key content

# Set proper permissions
sudo chmod 644 /etc/nginx/ssl/jerryagenyi.xyz/origin.pem
sudo chmod 600 /etc/nginx/ssl/jerryagenyi.xyz/origin.key
```

### Configure Nginx for Cloudflare

```bash
# Edit your server block
sudo nano /etc/nginx/sites-available/jerryagenyi.xyz
```

Add this configuration for Full (Strict) SSL:
```nginx
server {
    listen 443 ssl;
    server_name jerryagenyi.xyz www.jerryagenyi.xyz;

    ssl_certificate /etc/nginx/ssl/jerryagenyi.xyz/origin.pem;
    ssl_certificate_key /etc/nginx/ssl/jerryagenyi.xyz/origin.key;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;

    root /var/www/jerryagenyi.xyz/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name jerryagenyi.xyz www.jerryagenyi.xyz;
    return 301 https://$host$request_uri;
}
```

### Verify Cloudflare IPs for Security

To ensure only Cloudflare can connect to your origin server:

```bash
# Install jq for JSON processing
sudo apt install -y jq curl

# Create a script to update Cloudflare IPs
sudo nano /usr/local/bin/update-cloudflare-ips.sh
```

Add this content:
```bash
#!/bin/bash
# Get Cloudflare IPs
IPV4=$(curl -s https://api.cloudflare.com/client/v4/ips | jq -r '.result.ipv4_cidrs[]')
IPV6=$(curl -s https://api.cloudflare.com/client/v4/ips | jq -r '.result.ipv6_cidrs[]')

# Clear existing rules
sudo ufw delete allow 80/tcp
sudo ufw delete allow 443/tcp

# Allow SSH (keep your custom port)
# sudo ufw allow 2222/tcp

# Allow Cloudflare IPs
for IP in $IPV4; do
  sudo ufw allow from $IP to any port 80 proto tcp
  sudo ufw allow from $IP to any port 443 proto tcp
done

for IP in $IPV6; do
  sudo ufw allow from $IP to any port 80 proto tcp
  sudo ufw allow from $IP to any port 443 proto tcp
done

# Reload UFW
sudo ufw reload
```

Make it executable and run it:
```bash
sudo chmod +x /usr/local/bin/update-cloudflare-ips.sh
sudo /usr/local/bin/update-cloudflare-ips.sh

# Add to crontab to run weekly
echo "0 0 * * 0 /usr/local/bin/update-cloudflare-ips.sh > /var/log/cloudflare-update.log 2>&1" | sudo tee -a /etc/crontab
```

### Option 2: Let's Encrypt (Alternative if not using Cloudflare SSL)

```bash
# Install Certbot and Nginx plugin
sudo apt install -y certbot python3-certbot-nginx

# Obtain SSL certificate
sudo certbot --nginx -d jerryagenyi.xyz -d www.jerryagenyi.xyz

# Test auto-renewal
sudo certbot renew --dry-run
```

[↑ Return to Top](#table-of-contents)

## 7. Monitoring and Maintenance

### Install Monitoring Tools
```bash
# Install basic monitoring tools
sudo apt install -y htop iotop iftop fail2ban logwatch

# Configure Fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

### Set Up Regular Updates
```bash
# Create update script
sudo nano /usr/local/bin/system-update.sh
```

Add this content:
```bash
#!/bin/bash
apt update
apt upgrade -y
apt autoremove -y
apt autoclean
```

Make it executable and schedule it:
```bash
sudo chmod +x /usr/local/bin/system-update.sh

# Add to crontab to run weekly
echo "0 2 * * 0 /usr/local/bin/system-update.sh > /var/log/system-update.log 2>&1" | sudo tee -a /etc/crontab
```

[↑ Return to Top](#table-of-contents)

## 8. Backup Strategy

### Set Up Regular Backups
```bash
# Install backup tool
sudo apt install -y rsync

# Create backup script
sudo nano /usr/local/bin/backup.sh
```

Add this content:
```bash
#!/bin/bash
BACKUP_DIR="/backup/$(date +%Y-%m-%d)"
mkdir -p $BACKUP_DIR

# Backup web files
rsync -avz /var/www/ $BACKUP_DIR/www/

# Backup Nginx configuration
rsync -avz /etc/nginx/ $BACKUP_DIR/nginx/

# Backup database (if applicable)
# mysqldump -u root -p --all-databases > $BACKUP_DIR/all_databases.sql
```

Make it executable and schedule it:
```bash
sudo chmod +x /usr/local/bin/backup.sh

# Add to crontab to run daily
echo "0 1 * * * /usr/local/bin/backup.sh > /var/log/backup.log 2>&1" | sudo tee -a /etc/crontab
```

[↑ Return to Top](#table-of-contents)

## 9. WSL-Specific Considerations

### Installing and Setting Up WSL Using System PowerShell

If you're having issues with WSL installation through VSCode's terminal or other IDEs that can't access PowerShell properly, you can use the system PowerShell directly:

1. **Install WSL using System PowerShell**:
   - Press `Win + X` and select "Windows PowerShell (Admin)" or "Terminal (Admin)"
   - Run the following command to install WSL with Ubuntu:
     ```powershell
     wsl --install -d Ubuntu
     ```
   - This will install both WSL and Ubuntu. Your system will likely need to restart.
   - After restart, Ubuntu will launch automatically and prompt you to create a username and password.

2. **Verify WSL Installation**:
   - Open PowerShell and run:
     ```powershell
     wsl --list --verbose
     ```
   - You should see Ubuntu listed with version 2.

3. **Access Ubuntu from PowerShell**:
   - Simply type `wsl` in PowerShell to enter the Ubuntu environment
   - Or use `wsl -d Ubuntu` if you have multiple distributions

4. **Setting Up SSH in WSL for Remote Access**:
   - Enter your WSL Ubuntu environment:
     ```powershell
     wsl
     ```
   - Install the SSH server:
     ```bash
     sudo apt update
     sudo apt install openssh-server
     ```
   - Configure SSH:
     ```bash
     sudo nano /etc/ssh/sshd_config
     ```
   - Make these changes:
     - Change default port: `Port 2222` (choose a non-standard port)
     - Allow password authentication temporarily: `PasswordAuthentication yes`
   - Start the SSH service:
     ```bash
     sudo service ssh start
     ```
   - Create SSH keys:
     ```bash
     ssh-keygen -t ed25519 -C "jerryagenyi@gmail.com"
     ```

5. **Configure SSH Access from VSCode**:
   - Install the "Remote - SSH" extension in VSCode
   - Add a new SSH host configuration in VSCode:
     - Press `F1` and type "Remote-SSH: Add New SSH Host"
     - Enter: `ssh username@localhost -p 2222`
     - Select your SSH configuration file location
   - Connect to your WSL instance through SSH

### Service Management in WSL
- WSL doesn't use systemd, so commands like `systemctl` won't work directly
- Use the service command instead: `sudo service nginx start/stop/restart`
- For services that don't have a service script, you may need to start them manually

### Configuring WSL to Start Automatically with Windows

By default, WSL doesn't start automatically when Windows boots. To configure automatic startup:

1. **Create a Windows Startup Script**:
   - Create a new PowerShell script in your Windows startup folder:
   - Press `Win + R`, type `shell:startup` and press Enter
   - Right-click in the folder, select New > Text Document
   - Rename it to `start-wsl.ps1`
   - Edit the file and add:
     ```powershell
     # Start WSL
     wsl -d Ubuntu -u root service ssh start
     # Optional: Start specific distribution if you have multiple
     # wsl -d Ubuntu
     ```

2. **Create a Windows Task Scheduler Task** (more reliable method):
   - Press `Win + R`, type `taskschd.msc` and press Enter
   - Click "Create Basic Task" in the right panel
   - Name it "Start WSL" and click Next
   - Select "When I log on" and click Next
   - Select "Start a program" and click Next
   - In Program/script, enter: `powershell.exe`
   - In Arguments, enter: `-ExecutionPolicy Bypass -File "C:\path\to\your\start-wsl.ps1"`
   - Click Next, then Finish

### Automatic Service Startup Within WSL

Create a startup script to launch services when WSL starts:

```bash
# Create a startup script
sudo nano /etc/wsl-init.sh
```

Add this content:
```bash
#!/bin/bash
# Start services
service ssh start
service nginx start
# Add other services as needed
# service mysql start
# service redis-server start

# For Llama or other ML models
# cd /path/to/llama && ./start-llama.sh &
```

Make it executable:
```bash
sudo chmod +x /etc/wsl-init.sh
```

Add to your ~/.bashrc to run on startup:
```bash
echo "# Run startup services" >> ~/.bashrc
echo "if [ -f /etc/wsl-init.sh ]; then" >> ~/.bashrc
echo "  sudo /etc/wsl-init.sh" >> ~/.bashrc
echo "fi" >> ~/.bashrc
```

### Setting Up Llama for External Access

Llama is a powerful language model that can be run locally. To make it accessible from external applications:

#### 1. Install Llama

First, install Llama and its dependencies:

```bash
# Install build dependencies
sudo apt update
sudo apt install -y build-essential cmake git python3-dev python3-pip

# Clone the llama.cpp repository
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp

# Build the project
make

# Download a model (example: Llama 2 7B quantized)
# Note: You may need to accept terms on the Hugging Face website first
mkdir -p models
python3 -m pip install -U huggingface_hub
python3 scripts/download-model.py TheBloke/Llama-2-7B-GGUF
```

#### 2. Create a Startup Script for Llama

Create a dedicated startup script that configures Llama to accept external connections:

```bash
# Create a Llama startup script
sudo nano /usr/local/bin/start-llama.sh
```

Add this content (adjust paths and parameters as needed):
```bash
#!/bin/bash
# Start Llama service with external access configuration

# Navigate to Llama directory
cd /home/yourusername/llama.cpp

# Start the server with these important flags:
# --host 0.0.0.0     : Accept connections from any IP (not just localhost)
# --port 8080        : The port to listen on
# -m <model>         : Path to the model file
# -c 4096            : Context size
# --api-key "sk-..." : Optional API key for authentication
./server -m models/llama-2-7b.Q4_K_M.gguf -c 4096 --host 0.0.0.0 --port 8080 &

echo "Llama started on port 8080 and accessible from external networks"
```

Make it executable:
```bash
sudo chmod +x /usr/local/bin/start-llama.sh
```

#### 3. Configure Llama to Start Automatically

Add Llama to your WSL startup script:

```bash
sudo nano /etc/wsl-init.sh
# Add this line at the end:
/usr/local/bin/start-llama.sh
```

#### 4. Secure Your Llama API (Recommended)

For production use, add authentication to your Llama API:

```bash
# Generate a secure API key
API_KEY=$(openssl rand -hex 16)
echo "Your Llama API key is: $API_KEY"

# Edit your start script to include the API key
sudo nano /usr/local/bin/start-llama.sh
# Add --api-key "$API_KEY" to the server command
```

#### 5. Exposing Llama API Externally

##### Our Chosen Solution: Cloudflare Tunnel for Llama API

We are using Cloudflare Tunnel to expose our Llama API externally, as it provides secure, always-on access without requiring router configuration:

To expose your Llama API securely through Cloudflare Tunnel:

1. **Update your Cloudflare Tunnel configuration**:

```bash
nano ~/.cloudflared/config.yml
```

Add Llama API to your ingress rules:

```yaml
tunnel: YOUR_TUNNEL_ID
credentials-file: /home/yourusername/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  # SSH access
  - hostname: ssh.yourdomain.com
    service: ssh://localhost:1982

  # Llama API access
  - hostname: llama.yourdomain.com
    service: http://localhost:8080

  # Catch-all rule
  - service: http_status:404
```

2. **Add DNS record for Llama API**:

```bash
cloudflared tunnel route dns wsl-tunnel llama.yourdomain.com
```

3. **Restart Cloudflare Tunnel**:

```bash
sudo systemctl restart cloudflared
```

4. **Test your Llama API through Cloudflare**:

```bash
# From any device, anywhere
curl -X POST https://llama.yourdomain.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -d '{
    "model": "llama-2-7b",
    "messages": [{"role": "user", "content": "Hello, how are you?"}],
    "temperature": 0.7
  }'
```

5. **Add Authentication (Optional but Recommended)**:

You can add Cloudflare Access to require authentication before reaching your Llama API:

- In Cloudflare Dashboard, go to Zero Trust → Access → Applications
- Create a new application for `llama.yourdomain.com`
- Set up authentication methods (Google, GitHub, email one-time codes, etc.)
- This adds an additional security layer beyond your API key

### Port Forwarding from Windows to WSL
To make your WSL services accessible from outside your computer:

1. Find your WSL IP address:
```bash
ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'
```

2. Set up port forwarding in Windows (run in PowerShell as Administrator):
```powershell
# Replace 172.x.x.x with your WSL IP from above
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=80 connectaddress=172.x.x.x connectport=80
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=443 connectaddress=172.x.x.x connectport=443
# For your direct N8N access
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=172.x.x.x connectport=8080
```

3. Allow these ports in Windows Firewall:
```powershell
# Allow HTTP
New-NetFirewallRule -DisplayName "WSL-HTTP" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 80
# Allow HTTPS
New-NetFirewallRule -DisplayName "WSL-HTTPS" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 443
# Allow direct access port
New-NetFirewallRule -DisplayName "WSL-Direct" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8080
```

### Handling WSL IP Changes
WSL IP can change on restart. Create a script to update port forwarding:

```bash
# Create a script to update Windows port forwarding
sudo nano /usr/local/bin/update-wsl-ports.sh
```

Add this content:
```bash
#!/bin/bash
# Get WSL IP
WSL_IP=$(ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')

# Create PowerShell script
cat > /mnt/c/Users/$(cmd.exe /c echo %USERNAME% 2>/dev/null | tr -d '\r')/update-wsl-ports.ps1 << EOF
# Remove existing rules
netsh interface portproxy reset

# Add new rules with current WSL IP
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=80 connectaddress=$WSL_IP connectport=80
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=443 connectaddress=$WSL_IP connectport=443
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=$WSL_IP connectport=8080
EOF

# Run the PowerShell script as Administrator
powershell.exe -Command "Start-Process PowerShell -ArgumentList '-File C:\\Users\\$(cmd.exe /c echo %USERNAME% 2>/dev/null | tr -d '\r')\\update-wsl-ports.ps1' -Verb RunAs"
```

Make it executable:
```bash
sudo chmod +x /usr/local/bin/update-wsl-ports.sh
```

Add to your ~/.bashrc to run on startup:
```bash
echo "# Update Windows port forwarding on WSL start" >> ~/.bashrc
echo "if [ -f /usr/local/bin/update-wsl-ports.sh ]; then" >> ~/.bashrc
echo "  sudo /usr/local/bin/update-wsl-ports.sh" >> ~/.bashrc
echo "fi" >> ~/.bashrc
```

### Accessing Your WSL Server

#### Local Access
- From your Windows machine: http://localhost/
- From other devices on your network: http://your-windows-ip/

#### Setting Up Remote Access from Anywhere

To make your WSL server accessible from external networks (for SSH, web services, or Llama API):

##### 1. Configure Port Forwarding on Windows Host

This forwards traffic from Windows to your WSL instance. These commands **MUST** be run in an Administrator PowerShell:

```powershell
# IMPORTANT: You must run PowerShell as Administrator for these commands
# Right-click on PowerShell and select "Run as administrator"

# First, get your WSL IP address
# If you're in WSL bash shell, exit first by typing 'exit'
$wslIP = wsl -d Ubuntu-24.04 -e bash -c "ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'"
Write-Host "Your WSL IP is: $wslIP"

# If the above command doesn't work, you can manually find your WSL IP by running this in WSL:
# ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'
# Then use that IP address in the commands below

# Set up port forwarding for SSH (adjust port if you changed the default)
# Replace 2222 with your actual SSH port (e.g., 1982)
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=2222 connectaddress=$wslIP connectport=2222

# Set up port forwarding for HTTP (port 80)
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=80 connectaddress=$wslIP connectport=80

# Set up port forwarding for HTTPS (port 443)
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=443 connectaddress=$wslIP connectport=443

# Set up port forwarding for Llama API (adjust port as needed)
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=$wslIP connectport=8080

# Allow these ports through Windows Firewall
# These commands also require Administrator privileges
New-NetFirewallRule -DisplayName "WSL-SSH" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 2222
New-NetFirewallRule -DisplayName "WSL-HTTP" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 80
New-NetFirewallRule -DisplayName "WSL-HTTPS" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 443
New-NetFirewallRule -DisplayName "WSL-Llama" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8080

# View all port forwarding rules
netsh interface portproxy show all
```

##### Alternative: Create a PowerShell Script for Port Forwarding

If you prefer, you can create a script and run it as administrator:

1. Create a file named `setup-wsl-forwarding.ps1` with this content:
```powershell
# Get WSL IP
$wslIP = wsl -d Ubuntu-24.04 -e bash -c "ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'"
Write-Host "WSL IP: $wslIP"

# Set up port forwarding (adjust ports as needed)
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=1982 connectaddress=$wslIP connectport=1982
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=80 connectaddress=$wslIP connectport=80
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=443 connectaddress=$wslIP connectport=443
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=$wslIP connectport=8080

# Add firewall rules
New-NetFirewallRule -DisplayName "WSL-SSH" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 1982
New-NetFirewallRule -DisplayName "WSL-HTTP" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 80
New-NetFirewallRule -DisplayName "WSL-HTTPS" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 443
New-NetFirewallRule -DisplayName "WSL-Llama" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8080

Write-Host "Port forwarding and firewall rules set up successfully"
```

2. Right-click the file and select "Run with PowerShell as Administrator"

##### 2. Set Up Dynamic DNS (for Changing Home IP)

Most home internet connections have dynamic IP addresses that change periodically. To maintain consistent access:

1. Sign up for a free dynamic DNS service:
   - [No-IP](https://www.noip.com/)
   - [DuckDNS](https://www.duckdns.org/)
   - [Dynu](https://www.dynu.com/)

2. Install their update client on your Windows machine
   - This will keep your domain (e.g., `yourname.ddns.net`) pointing to your current IP

3. Configure the update client to run at startup

##### 3. External Access Options

###### Our Chosen Solution: Cloudflare Tunnel for Always-On Access

We are using **Cloudflare Tunnel** for our setup because it provides secure, always-on remote access without requiring router configuration, which is ideal for shared housing environments.

**Prerequisites**:
- A domain name (can be a free subdomain from Freenom if needed)
- A free Cloudflare account with your domain added

**Setup Steps**:
```bash
# Install cloudflared in WSL
curl -L --output cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared.deb
sudo apt-get install -f

# Log in to Cloudflare (this will open a browser)
cloudflared tunnel login

# Create a tunnel (replace 'wsl-tunnel' with your preferred name)
cloudflared tunnel create wsl-tunnel

# Configure your tunnel (create a config file)
mkdir -p ~/.cloudflared
nano ~/.cloudflared/config.yml
```

Add this to the config file:
```yaml
tunnel: YOUR_TUNNEL_ID  # ID from the create command output
credentials-file: /home/yourusername/.cloudflared/YOUR_TUNNEL_ID.json

# For SSH access
ingress:
  - hostname: ssh.yourdomain.com
    service: ssh://localhost:1982
  - service: http_status:404
```

Create a systemd service to run the tunnel automatically:
```bash
sudo nano /etc/systemd/system/cloudflared.service
```

Add this content:
```ini
[Unit]
Description=Cloudflare Tunnel
After=network.target

[Service]
Type=simple
User=yourusername
ExecStart=/usr/bin/cloudflared tunnel run wsl-tunnel
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Enable and start the service:
```bash
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
```

Add DNS record in Cloudflare dashboard:
```bash
# Get the command to add DNS records
cloudflared tunnel route dns wsl-tunnel ssh.yourdomain.com
```

**Accessing Your WSL Server**:
```bash
# SSH from anywhere
ssh -p 22 yourusername@ssh.yourdomain.com
```

**Security Benefits**:
- No open ports on your router
- End-to-end encryption
- DDoS protection
- Optional Zero Trust access controls

###### Alternative Options

If Cloudflare Tunnel doesn't meet your needs, here are other options:

**Option A: Router Port Forwarding** (If You Have Router Access)
1. Log into your router's admin panel (typically 192.168.1.1 or 192.168.0.1)
2. Find "Port Forwarding" or "Virtual Server" settings
3. Add rules to forward these ports to your Windows PC's internal IP:
   - External port 2222 → Internal port 2222 (SSH)
   - External port 80 → Internal port 80 (HTTP)
   - External port 443 → Internal port 443 (HTTPS)
   - External port 8080 → Internal port 8080 (Llama API)

**Option B: Reverse SSH Tunnels** (No Router Access Required)
```bash
# If you have a public server (VPS, etc.)
ssh -R 2222:localhost:1982 username@your-public-server.com

# Free tunneling services:
ssh -R 80:localhost:1982 serveo.net
# or
ssh -R 80:localhost:1982 localhost.run
```

**Option C: VPN Services** (No Router Access Required)
- **Tailscale**: [https://tailscale.com/](https://tailscale.com/)
- **ZeroTier**: [https://www.zerotier.com/](https://www.zerotier.com/)

**Option D: Other Cloud Tunneling Services** (No Router Access Required)
- **ngrok**: [https://ngrok.com/](https://ngrok.com/)

##### 4. Accessing Your Services Remotely

Once set up, you can access your services from anywhere:

```bash
# With router port forwarding + dynamic DNS
ssh -p 2222 yourusername@yourname.ddns.net

# With ngrok (use the address provided by ngrok)
ssh -p 12345 yourusername@0.tcp.ngrok.io

# With Tailscale (use the tailscale IP)
ssh -p 1982 yourusername@your-machine-tailscale-ip

# Web access (with router port forwarding)
http://yourname.ddns.net
https://yourname.ddns.net

# Llama API access (with router port forwarding)
http://yourname.ddns.net:8080
```

##### 5. Security Considerations for Remote Access

When exposing services to the internet:

1. **Keep everything updated**: `sudo apt update && sudo apt upgrade -y`
2. **Use strong SSH configuration**: Follow all SSH hardening steps in this guide
3. **Consider a VPN**: For more sensitive services, use a VPN instead of direct exposure
4. **Set up fail2ban**: `sudo apt install fail2ban` to block brute force attempts
5. **Monitor logs regularly**: Check `/var/log/auth.log` for suspicious activity

### Troubleshooting Common WSL Issues

#### Installation Issues
1. **WSL Command Not Found**:
   - Ensure Windows Subsystem for Linux is enabled in Windows Features
   - Open PowerShell as Administrator and run:
     ```powershell
     dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
     dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
     ```
   - Restart your computer
   - Download and install the WSL2 Linux kernel update from Microsoft

2. **Ubuntu Installation Fails**:
   - Try installing from the Microsoft Store instead
   - If that fails, download the Ubuntu appx package directly from Microsoft
   - Install with PowerShell:
     ```powershell
     Add-AppxPackage .\Ubuntu.appx
     ```

3. **WSL Version Issues**:
   - Set WSL 2 as default:
     ```powershell
     wsl --set-default-version 2
     ```
   - Convert existing distribution to WSL 2:
     ```powershell
     wsl --set-version Ubuntu 2
     ```

#### Networking Issues
1. **Can't Access Internet from WSL**:
   - Check Windows Firewall settings
   - Try resetting the WSL network:
     ```powershell
     netsh winsock reset
     ```
   - Restart your computer

2. **Can't Access WSL Server from Windows**:
   - Verify the WSL IP address:
     ```bash
     ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'
     ```
   - Ensure port forwarding is set up correctly
   - Check Windows Firewall rules

#### Performance Issues
1. **Slow File System Performance**:
   - Avoid working with files across the WSL/Windows boundary
   - Keep project files within the Linux file system
   - Use `/home/username/` instead of `/mnt/c/`

2. **High Memory Usage**:
   - Limit WSL memory usage by creating a `.wslconfig` file in your Windows user directory:
     ```
     [wsl2]
     memory=4GB
     processors=2
     ```

### Accessing WSL from Different IDEs

Different IDEs have varying levels of WSL integration. Here are approaches for common development environments:

#### VSCode
1. **Direct WSL Integration** (Preferred):
   - Install the "Remote - WSL" extension
   - Click the green remote button in the bottom-left corner
   - Select "New WSL Window" or your specific distribution
   - VSCode will open a new window connected directly to WSL

2. **SSH Connection** (Alternative when direct integration fails):
   - Set up SSH in WSL as described in the installation section
   - Use the "Remote - SSH" extension to connect to your WSL instance
   - Connect using `localhost` with your custom SSH port

#### JetBrains IDEs (IntelliJ, PyCharm, etc.)
1. **WSL Integration**:
   - Go to Settings/Preferences → Tools → Terminal
   - Set Shell path to: `wsl.exe`
   - For specific distribution: `wsl.exe -d Ubuntu`

2. **SSH Connection**:
   - Set up SSH in WSL as described earlier
   - Use Tools → Deployment → Configuration
   - Add a new SFTP connection to localhost with your custom SSH port

#### Other IDEs without WSL Support
1. **SSH Connection**:
   - Set up SSH in WSL as described earlier
   - Configure your IDE to connect via SSH to localhost with your custom port
   - Use SFTP/SCP for file transfers if needed

2. **Shared Folders**:
   - WSL can access Windows files at `/mnt/c/` (for C: drive)
   - Windows can access WSL files at `\\wsl$\Ubuntu\` in File Explorer
   - Configure your IDE to work with files in a shared location

[↑ Return to Top](#table-of-contents)

## 10. Advanced: Containerization with Docker

### Install Docker and Docker Compose

```bash
# Update package index
sudo apt update

# Install prerequisites
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common gnupg

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add your user to the docker group
sudo usermod -aG docker $USER

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Verify installations
docker --version
docker-compose --version
```

### Create Docker Network

```bash
# Create a network for your containers
docker network create agentic-network
```

### Create Docker Compose Configuration

Create a `docker-compose.yml` file in your project directory:

```bash
mkdir -p ~/agentic-platform
cd ~/agentic-platform
nano docker-compose.yml
```

Add the following content (we'll expand this as we add more services):

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    container_name: nginx-proxy
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/conf:/etc/nginx/conf.d
      - ./nginx/ssl:/etc/nginx/ssl
      - ./nginx/www:/var/www/html
      - ./nginx/logs:/var/log/nginx
    networks:
      - agentic-network

networks:
  agentic-network:
    external: true
```

[↑ Return to Top](#table-of-contents)

## 11. Advanced: Supabase Setup

### Deploy Supabase Locally

```bash
# Clone Supabase repository
git clone https://github.com/supabase/supabase
cd supabase/docker

# Copy example env file
cp .env.example .env

# Edit the environment variables
nano .env
```

Modify the `.env` file with secure credentials:

```
# Supabase configuration
POSTGRES_PASSWORD=your_secure_postgres_password
JWT_SECRET=your_secure_jwt_secret
ANON_KEY=your_secure_anon_key
SERVICE_ROLE_KEY=your_secure_service_role_key
```

Add Supabase to your docker-compose.yml:

```yaml
services:
  # ... existing services

  supabase:
    image: supabase/supabase-local
    container_name: supabase
    restart: always
    env_file:
      - ./supabase/.env
    volumes:
      - ./supabase/volumes/db/data:/var/lib/postgresql/data
      - ./supabase/volumes/storage:/var/lib/storage
    ports:
      - "54321:54321" # Studio
      - "54322:54322" # API
    networks:
      - agentic-network
```

[↑ Return to Top](#table-of-contents)

## 12. Advanced: N8N Configuration

### Deploy N8N with Docker

Add N8N to your docker-compose.yml:

```yaml
services:
  # ... existing services

  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=n8n.jerryagenyi.xyz
      - N8N_PORT=5678
      - N8N_PROTOCOL=https
      - N8N_ENCRYPTION_KEY=your_secure_encryption_key
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=supabase
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=postgres
      - DB_POSTGRESDB_PASSWORD=your_secure_postgres_password
      - N8N_LOG_LEVEL=info
    volumes:
      - ./n8n/data:/home/node/.n8n
    depends_on:
      - supabase
    networks:
      - agentic-network
```

### Configure Nginx for N8N

Create an Nginx configuration for N8N:

```bash
mkdir -p ~/agentic-platform/nginx/conf
nano ~/agentic-platform/nginx/conf/n8n.conf
```

Add this configuration:

```nginx
server {
    listen 443 ssl;
    server_name n8n.jerryagenyi.xyz;

    ssl_certificate /etc/nginx/ssl/jerryagenyi.xyz/origin.pem;
    ssl_certificate_key /etc/nginx/ssl/jerryagenyi.xyz/origin.key;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;

    # Add Cloudflare headers
    real_ip_header CF-Connecting-IP;

    # Log settings
    access_log /var/log/nginx/n8n.access.log;
    error_log /var/log/nginx/n8n.error.log;

    location / {
        proxy_pass http://n8n:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Timeout settings
        proxy_read_timeout 90;
        proxy_connect_timeout 90;
        proxy_send_timeout 90;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name n8n.jerryagenyi.xyz;
    return 301 https://$host$request_uri;
}
```

### Direct Access Configuration for N8N

Create a configuration for direct access (bypassing Cloudflare):

```bash
nano ~/agentic-platform/nginx/conf/n8n-direct.conf
```

Add this configuration:

```nginx
server {
    listen 8080;
    server_name direct.n8n.jerryagenyi.xyz;

    # Optional: IP restriction to only allow your Google VM
    # Replace with your Google VM's IP address
    allow 35.X.X.X;  # Your Google VM IP
    deny all;

    location / {
        proxy_pass http://n8n:5678;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket support
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Timeout settings
        proxy_read_timeout 90;
        proxy_connect_timeout 90;
        proxy_send_timeout 90;
    }
}
```

[↑ Return to Top](#table-of-contents)

## 13. Advanced: Security Hardening

### Secrets Management

Install and configure HashiCorp Vault for secrets management:

```bash
# Add Vault to docker-compose.yml
nano ~/agentic-platform/docker-compose.yml
```

Add this to your docker-compose.yml:

```yaml
services:
  # ... existing services

  vault:
    image: vault:latest
    container_name: vault
    restart: always
    cap_add:
      - IPC_LOCK
    ports:
      - "8200:8200"
    environment:
      - VAULT_DEV_ROOT_TOKEN_ID=your_secure_root_token
      - VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200
    volumes:
      - ./vault/data:/vault/data
      - ./vault/logs:/vault/logs
      - ./vault/config:/vault/config
    networks:
      - agentic-network
```

### Network Security

Implement network segmentation and security:

```bash
# Create a more secure Docker network configuration
docker network create --subnet=172.20.0.0/16 --gateway=172.20.0.1 agentic-secure-network

# Update your docker-compose.yml to use this network
```

### Web Application Firewall

Add ModSecurity WAF to your Nginx setup:

```bash
# Update the Nginx service in docker-compose.yml
```

Replace the Nginx service with:

```yaml
nginx:
  image: owasp/modsecurity-crs:nginx
  container_name: nginx-waf
  restart: always
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - ./nginx/conf:/etc/nginx/conf.d
    - ./nginx/ssl:/etc/nginx/ssl
    - ./nginx/www:/var/www/html
    - ./nginx/logs:/var/log/nginx
    - ./nginx/modsec:/etc/nginx/modsec
  environment:
    - PARANOIA=1
    - ANOMALY_INBOUND=10
    - ANOMALY_OUTBOUND=5
  networks:
    - agentic-network
```

### Regular Security Audits

Set up automated security scanning:

```bash
# Create a security audit script
mkdir -p ~/agentic-platform/scripts
nano ~/agentic-platform/scripts/security-audit.sh
```

Add this content:

```bash
#!/bin/bash
# Security audit script

# Update all containers
docker-compose pull
docker-compose up -d

# Scan for vulnerabilities
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image n8nio/n8n:latest
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nginx:latest
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image supabase/supabase-local

# Check for exposed secrets
docker run --rm -v $(pwd):/src zricethezav/gitleaks:latest detect --source="/src" --verbose

# Log results
echo "Security audit completed on $(date)" >> ~/agentic-platform/logs/security-audit.log
```

Make it executable and schedule it:

```bash
chmod +x ~/agentic-platform/scripts/security-audit.sh
echo "0 2 * * 0 ~/agentic-platform/scripts/security-audit.sh" | crontab -
```

[↑ Return to Top](#table-of-contents)

## 14. Advanced: Observability Stack

### Monitoring with Prometheus and Grafana

Add monitoring services to your docker-compose.yml:

```yaml
services:
  # ... existing services

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: always
    volumes:
      - ./prometheus/config:/etc/prometheus
      - ./prometheus/data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"
    networks:
      - agentic-network

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: always
    volumes:
      - ./grafana/data:/var/lib/grafana
      - ./grafana/provisioning:/etc/grafana/provisioning
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=your_secure_grafana_password
      - GF_USERS_ALLOW_SIGN_UP=false
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
    networks:
      - agentic-network
```

Create a Prometheus configuration:

```bash
mkdir -p ~/agentic-platform/prometheus/config
nano ~/agentic-platform/prometheus/config/prometheus.yml
```

Add this content:

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']

  - job_name: 'n8n'
    metrics_path: /metrics
    static_configs:
      - targets: ['n8n:5678']
```

Add container metrics collectors:

```yaml
services:
  # ... existing services

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: always
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.ignored-mount-points=^/(sys|proc|dev|host|etc)($$|/)'
    ports:
      - "9100:9100"
    networks:
      - agentic-network

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    restart: always
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
    ports:
      - "8080:8080"
    networks:
      - agentic-network
```

### Centralized Logging with ELK Stack

Add ELK stack to your docker-compose.yml:

```yaml
services:
  # ... existing services

  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.0
    container_name: elasticsearch
    restart: always
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - ./elasticsearch/data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - agentic-network

  logstash:
    image: docker.elastic.co/logstash/logstash:7.17.0
    container_name: logstash
    restart: always
    volumes:
      - ./logstash/config:/usr/share/logstash/config
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    depends_on:
      - elasticsearch
    networks:
      - agentic-network

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.0
    container_name: kibana
    restart: always
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_URL=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - agentic-network
```

Configure Logstash for N8N logs:

```bash
mkdir -p ~/agentic-platform/logstash/config
mkdir -p ~/agentic-platform/logstash/pipeline

# Create logstash.yml
nano ~/agentic-platform/logstash/config/logstash.yml
```

Add this content:

```yaml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]
```

Create a pipeline configuration:

```bash
nano ~/agentic-platform/logstash/pipeline/logstash.conf
```

Add this content:

```
input {
  beats {
    port => 5044
  }

  tcp {
    port => 5000
    codec => json
  }
}

filter {
  if [container_name] == "n8n" {
    mutate {
      add_field => { "[@metadata][target_index]" => "n8n-%{+YYYY.MM.dd}" }
    }
  } else {
    mutate {
      add_field => { "[@metadata][target_index]" => "docker-%{+YYYY.MM.dd}" }
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "%{[@metadata][target_index]}"
  }
}
```

[↑ Return to Top](#table-of-contents)

## 15. Advanced: Workflow Management

### Git Integration for N8N Workflows

Set up Git integration for N8N workflows:

```bash
# Create a directory for N8N workflows
mkdir -p ~/agentic-platform/n8n/workflows

# Initialize a Git repository
cd ~/agentic-platform/n8n/workflows
git init
git config user.name "jerryagenyi"
git config user.email "jerryagenyi@gmail.com"

# Create a .gitignore file
nano .gitignore
```

Add this content to .gitignore:

```
# N8N credentials
.n8n

# Environment variables
.env

# Logs
*.log
```

### CI/CD Pipeline for Workflows

Create a GitHub Actions workflow for your N8N workflows:

```bash
# Create GitHub Actions directory
mkdir -p ~/agentic-platform/n8n/workflows/.github/workflows
nano ~/agentic-platform/n8n/workflows/.github/workflows/deploy.yml
```

Add this content:

```yaml
name: Deploy N8N Workflows

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up SSH
        uses: webfactory/ssh-agent@v0.7.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Deploy to Production
        run: |
          ssh ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} "cd ~/agentic-platform && git pull && docker-compose restart n8n"
```

### Workflow Testing Framework

Create a testing framework for N8N workflows:

```bash
mkdir -p ~/agentic-platform/n8n/tests
nano ~/agentic-platform/n8n/tests/test-workflows.js
```

Add this content:

```javascript
const axios = require('axios');
const fs = require('fs');
const path = require('path');

// Configuration
const N8N_API_URL = 'https://n8n.jerryagenyi.xyz/api/v1';
const API_KEY = process.env.N8N_API_KEY;

// Test a specific workflow
async function testWorkflow(workflowId) {
  try {
    const response = await axios.post(
      `${N8N_API_URL}/workflows/${workflowId}/execute`,
      {},
      {
        headers: {
          'X-N8N-API-KEY': API_KEY
        }
      }
    );

    console.log(`Workflow ${workflowId} executed successfully:`, response.data);
    return true;
  } catch (error) {
    console.error(`Error executing workflow ${workflowId}:`, error.message);
    return false;
  }
}

// Main test runner
async function runTests() {
  // Get list of workflows to test
  const workflowIds = ['workflow1', 'workflow2']; // Replace with your workflow IDs

  let passed = 0;
  let failed = 0;

  for (const id of workflowIds) {
    const result = await testWorkflow(id);
    if (result) {
      passed++;
    } else {
      failed++;
    }
  }

  console.log(`Test results: ${passed} passed, ${failed} failed`);
  process.exit(failed > 0 ? 1 : 0);
}

runTests();
```

[↑ Return to Top](#table-of-contents)

## 16. Advanced: High Availability

### Load Balancing for N8N

Add a load balancer for multiple N8N instances:

```yaml
services:
  # ... existing services

  n8n-1:
    image: n8nio/n8n:latest
    container_name: n8n-1
    restart: always
    environment:
      - N8N_HOST=n8n.jerryagenyi.xyz
      - N8N_PORT=5678
      - N8N_PROTOCOL=https
      - N8N_ENCRYPTION_KEY=your_secure_encryption_key
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=supabase
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=postgres
      - DB_POSTGRESDB_PASSWORD=your_secure_postgres_password
      - N8N_LOG_LEVEL=info
    volumes:
      - ./n8n/data:/home/node/.n8n
    networks:
      - agentic-network

  n8n-2:
    image: n8nio/n8n:latest
    container_name: n8n-2
    restart: always
    environment:
      - N8N_HOST=n8n.jerryagenyi.xyz
      - N8N_PORT=5679
      - N8N_PROTOCOL=https
      - N8N_ENCRYPTION_KEY=your_secure_encryption_key
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=supabase
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=postgres
      - DB_POSTGRESDB_PASSWORD=your_secure_postgres_password
      - N8N_LOG_LEVEL=info
    volumes:
      - ./n8n/data:/home/node/.n8n
    networks:
      - agentic-network

  nginx-lb:
    image: nginx:latest
    container_name: nginx-lb
    restart: always
    ports:
      - "5678:5678"
    volumes:
      - ./nginx/lb/nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - n8n-1
      - n8n-2
    networks:
      - agentic-network
```

Create a load balancer configuration:

```bash
mkdir -p ~/agentic-platform/nginx/lb
nano ~/agentic-platform/nginx/lb/nginx.conf
```

Add this content:

```nginx
events {
    worker_connections 1024;
}

http {
    upstream n8n_backend {
        server n8n-1:5678;
        server n8n-2:5679;
    }

    server {
        listen 5678;

        location / {
            proxy_pass http://n8n_backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;

            # WebSocket support
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }
}
```

### Backup and Disaster Recovery

Create a comprehensive backup strategy for your N8N workflows and data:

```bash
# Create a backup script
mkdir -p ~/agentic-platform/scripts
nano ~/agentic-platform/scripts/backup-n8n.sh
```

Add this content:

```bash
#!/bin/bash
# N8N Backup Script

# Set variables
BACKUP_DIR="/backup/n8n/$(date +%Y-%m-%d)"
DOCKER_COMPOSE_DIR=~/agentic-platform
N8N_DATA_DIR=$DOCKER_COMPOSE_DIR/n8n/data
SUPABASE_DATA_DIR=$DOCKER_COMPOSE_DIR/supabase/volumes/db/data
BACKUP_RETENTION_DAYS=30

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup N8N workflows and data
echo "Backing up N8N data..."
tar -czf $BACKUP_DIR/n8n-data.tar.gz -C $DOCKER_COMPOSE_DIR n8n/data

# Backup database
echo "Backing up database..."
docker exec supabase pg_dump -U postgres -d n8n > $BACKUP_DIR/n8n-db.sql

# Backup docker-compose configuration
echo "Backing up configuration..."
cp $DOCKER_COMPOSE_DIR/docker-compose.yml $BACKUP_DIR/
cp -r $DOCKER_COMPOSE_DIR/nginx $BACKUP_DIR/

# Remove backups older than retention period
find /backup/n8n -type d -mtime +$BACKUP_RETENTION_DAYS -exec rm -rf {} \;

echo "Backup completed: $BACKUP_DIR"
```

Make it executable and schedule it:

```bash
chmod +x ~/agentic-platform/scripts/backup-n8n.sh

# Add to crontab to run daily
echo "0 2 * * * ~/agentic-platform/scripts/backup-n8n.sh > /var/log/n8n-backup.log 2>&1" | crontab -
```

### Automated Recovery

Create a recovery script:

```bash
nano ~/agentic-platform/scripts/restore-n8n.sh
```

Add this content:

```bash
#!/bin/bash
# N8N Recovery Script

# Check if backup date is provided
if [ -z "$1" ]; then
  echo "Usage: $0 YYYY-MM-DD"
  exit 1
fi

# Set variables
BACKUP_DATE=$1
BACKUP_DIR="/backup/n8n/$BACKUP_DATE"
DOCKER_COMPOSE_DIR=~/agentic-platform

# Check if backup exists
if [ ! -d "$BACKUP_DIR" ]; then
  echo "Backup not found: $BACKUP_DIR"
  exit 1
fi

# Stop services
cd $DOCKER_COMPOSE_DIR
docker-compose down

# Restore N8N data
echo "Restoring N8N data..."
rm -rf $DOCKER_COMPOSE_DIR/n8n/data
tar -xzf $BACKUP_DIR/n8n-data.tar.gz -C $DOCKER_COMPOSE_DIR

# Start services
docker-compose up -d supabase

# Wait for database to be ready
echo "Waiting for database to be ready..."
sleep 30

# Restore database
echo "Restoring database..."
cat $BACKUP_DIR/n8n-db.sql | docker exec -i supabase psql -U postgres -d n8n

# Restart all services
docker-compose down
docker-compose up -d

echo "Recovery completed from: $BACKUP_DATE"
```

Make it executable:

```bash
chmod +x ~/agentic-platform/scripts/restore-n8n.sh
```

[↑ Return to Top](#table-of-contents)

## Conclusion

This comprehensive guide provides you with both basic and advanced configurations for setting up an Ubuntu server with Nginx for web hosting and running production-grade N8N agentic workflows. By following these instructions, you can create a secure, scalable, and maintainable environment for your automation needs.

The guide covers:
- Essential server setup and security
- Web server configuration with Nginx
- SSL/TLS setup with Cloudflare
- Containerization with Docker
- Database management with Supabase
- N8N workflow configuration and management
- Advanced security hardening
- Monitoring and observability
- High availability and load balancing
- Backup and disaster recovery

Remember to adapt these instructions to your specific needs and regularly update your system to maintain security and performance.

[↑ Return to Top](#table-of-contents)