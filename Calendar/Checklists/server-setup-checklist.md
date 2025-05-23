# Ubuntu Server Setup Checklist

This checklist tracks the progress of setting up an Ubuntu server environment in WSL for development and hosting purposes. It follows the comprehensive guide in `ubuntu-server-complete-guide.md`.

## Current Status Summary
- ✅ WSL installed and running (Ubuntu-24.04)
- ✅ SSH server configured and tested (port 1982)
- ✅ Port forwarding and firewall rules set up
- ✅ Local SSH access working
- ⏳ Automatic startup configured (needs testing)
- ❌ Cloudflare Tunnel not yet set up
- ❌ Llama not yet installed

## WSL Installation and Configuration

- [x] Install WSL on Windows
- [x] Install Ubuntu distribution (Ubuntu-24.04)
- [x] Configure basic user account
- [x] Update and upgrade packages
- [x] Install essential tools and utilities

## SSH Configuration and Security

- [x] Install SSH server
- [x] Configure SSH port (changed to 1982)
- [x] Set up SSH security settings
  - [x] Disable root login (`PermitRootLogin no`)
  - [x] Configure allowed users (`AllowUsers yourusername`)
  - [ ] Set up SSH key authentication
  - [ ] Disable password authentication (after confirming key auth works)
- [x] Test SSH locally
- [x] Test SSH from Windows to WSL
- [x] Test SSH from another device on local network

## Automatic Startup Configuration

- [x] Create Task Scheduler task for WSL startup
  - [x] Create start-wsl.ps1 script
  - [x] Configure Task Scheduler to run at login
  - [ ] Test automatic startup after reboot
- [ ] Create startup script for services within WSL
  - [ ] Create /etc/wsl-init.sh
  - [ ] Configure services to start automatically
  - [ ] Add to .bashrc for automatic execution

## External Access Setup

- [x] Configure port forwarding on Windows
  - [x] Forward SSH port (1982)
  - [ ] Forward HTTP port (80)
  - [ ] Forward HTTPS port (443)
- [x] Set up Windows Firewall rules
- [ ] Configure Cloudflare Tunnel for remote access
  - [ ] Install cloudflared
  - [ ] Log in to Cloudflare account
  - [ ] Create tunnel
  - [ ] Configure tunnel (config.yml)
  - [ ] Set up systemd service for automatic startup
  - [ ] Add DNS records
  - [ ] Test external SSH access

## Web Server Setup

- [ ] Install Nginx
- [ ] Configure basic website
- [ ] Set up SSL with Let's Encrypt
- [ ] Configure virtual hosts
- [ ] Test web server locally and remotely

## Llama Setup

- [ ] Install Llama and dependencies
  - [ ] Install build dependencies
  - [ ] Clone llama.cpp repository
  - [ ] Build the project
  - [ ] Download model files
- [ ] Configure Llama for external access
  - [ ] Create startup script
  - [ ] Configure for external connections
  - [ ] Set up API key for security
- [ ] Configure Cloudflare Tunnel for Llama API
  - [ ] Update Cloudflare Tunnel configuration
  - [ ] Add DNS record for Llama API
  - [ ] Restart Cloudflare Tunnel
- [ ] Test Llama API locally and remotely

## Monitoring and Maintenance

- [ ] Set up basic monitoring
- [ ] Configure log rotation
- [ ] Create backup strategy
- [ ] Document maintenance procedures

## Documentation

- [x] Update ubuntu-server-complete-guide.md with Cloudflare Tunnel instructions
- [x] Create server-setup-checklist.md (this file)
- [ ] Document server configuration details
- [ ] Create troubleshooting guide

## Next Steps (In Order)

1. **Test automatic startup**:
   - Restart computer
   - Verify SSH service starts automatically
   - Check with: `wsl -d Ubuntu-24.04 -e service ssh status`

2. **Set up Cloudflare Tunnel**:
   - Install cloudflared
   - Log in to Cloudflare
   - Create and configure tunnel
   - Set up systemd service
   - Add DNS records
   - Test external access

3. **Install and configure Llama**:
   - Follow Llama setup instructions
   - Configure for external access
   - Set up with Cloudflare Tunnel
   - Test API access

## Notes

- Current WSL IP: 172.20.158.121
- SSH port: 1982
- WSL Distribution: Ubuntu-24.04
- Cloudflare Tunnel will be used for external access instead of traditional port forwarding
- All services should be configured to start automatically when WSL starts
