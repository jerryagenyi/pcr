# ubuntu-server-setup-and-security-checklist

A step-by-step checklist for setting up and securing your Ubuntu server on WSL2. This covers all foundational steps and lays the groundwork for future advanced configurations.

---

## 1. Initial System Setup
- [ ] Update package lists: `sudo apt update`
- [ ] Upgrade installed packages: `sudo apt upgrade -y`
- [ ] Install essential packages: `sudo apt install -y build-essential curl file git`
- [ ] Set timezone:
    - [ ] Check current timezone: `timedatectl`
    - [ ] List available timezones: `timedatectl list-timezones`
    - [ ] Set your timezone: `sudo timedatectl set-timezone <Your/Timezone>`

## 2. User Management and SSH Security
- [ ] Create a new admin user: `sudo adduser <username>`
- [ ] Add user to sudo group: `sudo usermod -aG sudo <username>`
- [ ] Switch to new user: `su - <username>`
- [ ] Edit SSH config: `sudo nano /etc/ssh/sshd_config`
    - [ ] Change default port (e.g., `Port 2222`)
    - [ ] Disable root login: `PermitRootLogin no`
    - [ ] Limit user login: `AllowUsers <username>`
    - [ ] Disable password authentication: `PasswordAuthentication no`
    - [ ] Enable key authentication: `PubkeyAuthentication yes`
    - [ ] Set login grace time: `LoginGraceTime 30`
    - [ ] Set max auth tries: `MaxAuthTries 3`
- [ ] Restart SSH: `sudo systemctl restart sshd` (or `sudo service ssh restart` on WSL)
- [ ] Generate SSH key pair on your local machine: `ssh-keygen -t ed25519 -C "your_email@example.com"`
- [ ] Copy public key to server: `ssh-copy-id -i ~/.ssh/id_ed25519.pub <username>@<server_ip> -p <port>`

## 3. Firewall Configuration
- [ ] Install UFW: `sudo apt install -y ufw`
- [ ] Set default policies:
    - [ ] `sudo ufw default deny incoming`
    - [ ] `sudo ufw default allow outgoing`
- [ ] Allow SSH: `sudo ufw allow <port>/tcp`
- [ ] Allow HTTP: `sudo ufw allow 80/tcp`
- [ ] Allow HTTPS: `sudo ufw allow 443/tcp`
- [ ] Enable firewall: `sudo ufw enable`
- [ ] Check status: `sudo ufw status verbose`

## 4. System Hardening
- [ ] Secure shared memory:
    - [ ] Edit `/etc/fstab` and add: `tmpfs /run/shm tmpfs defaults,noexec,nosuid 0 0`
- [ ] Disable unused services:
    - [ ] List running: `systemctl list-units --type=service --state=running`
    - [ ] Disable/stop as needed (e.g., `sudo systemctl disable bluetooth.service`)
- [ ] Configure automatic security updates:
    - [ ] `sudo apt install -y unattended-upgrades apt-listchanges`
    - [ ] `sudo dpkg-reconfigure -plow unattended-upgrades`
- [ ] Harden network parameters:
    - [ ] Edit `/etc/sysctl.conf` and add:
        ```
        net.ipv4.conf.all.rp_filter = 1
        net.ipv4.conf.default.rp_filter = 1
        net.ipv4.icmp_echo_ignore_broadcasts = 1
        net.ipv4.conf.all.accept_source_route = 0
        net.ipv4.conf.default.accept_source_route = 0
        net.ipv4.conf.all.send_redirects = 0
        net.ipv4.conf.default.send_redirects = 0
        net.ipv4.tcp_syncookies = 1
        net.ipv4.tcp_max_syn_backlog = 2048
        net.ipv4.tcp_synack_retries = 2
        net.ipv4.tcp_syn_retries = 5
        ```
    - [ ] Apply changes: `sudo sysctl -p`

## 5. Nginx Web Server Setup
- [ ] Install Nginx: `sudo apt install -y nginx`
- [ ] Start Nginx: `sudo service nginx start`
- [ ] (WSL) Add Nginx auto-start to `~/.bashrc` if desired
- [ ] Create web root: `sudo mkdir -p /var/www/<your_domain>/html`
- [ ] Set permissions:
    - [ ] `sudo chown -R $USER:$USER /var/www/<your_domain>/html`
    - [ ] `sudo chmod -R 755 /var/www/<your_domain>`
- [ ] Create a sample `index.html` in your web root
- [ ] Create Nginx server block config in `/etc/nginx/sites-available/<your_domain>`
- [ ] Enable site:
    - [ ] `sudo ln -s /etc/nginx/sites-available/<your_domain> /etc/nginx/sites-enabled/`
    - [ ] Remove default site if desired: `sudo rm /etc/nginx/sites-enabled/default`
- [ ] Test config: `sudo nginx -t`
- [ ] Restart Nginx: `sudo service nginx restart`

## 6. SSL/TLS Configuration (Cloudflare Recommended)
- [ ] Decide on SSL mode (Flexible, Full, or Full Strict)
- [ ] (Full/Strict) Create `/etc/nginx/ssl/<your_domain>` and add Cloudflare Origin cert/key
- [ ] Set permissions: `sudo chmod 644 ...pem`, `sudo chmod 600 ...key`
- [ ] Update Nginx server block for SSL (listen 443, add cert paths, SSL settings)
- [ ] Redirect HTTP to HTTPS in Nginx
- [ ] (Optional) Use Let's Encrypt if not using Cloudflare SSL

## 7. Monitoring and Maintenance
- [ ] Install monitoring tools: `sudo apt install -y htop iotop iftop fail2ban logwatch`
- [ ] Configure Fail2ban: copy and edit jail.local
- [ ] Create and schedule update script for regular system updates

## 8. Backup Strategy
- [ ] Install rsync: `sudo apt install -y rsync`
- [ ] Create backup script for web files and Nginx config
- [ ] Make script executable and schedule with cron

## 9. WSL-Specific Considerations
- [ ] Use `service` command for service management (not `systemctl`)
- [ ] Create `/etc/wsl-init.sh` for auto-starting services
- [ ] Add script to `~/.bashrc` for auto-run on WSL start
- [ ] Set up port forwarding from Windows to WSL (use PowerShell `netsh` commands)
- [ ] Allow ports in Windows Firewall
- [ ] (Optional) Script to update port forwarding if WSL IP changes

---

**Tip:** Check off each item as you complete it. This checklist prepares your server for secure, reliable operation and is a solid foundation for future advanced setups (containerization, agentic workflows, etc.).