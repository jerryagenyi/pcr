## llm-server-setup-plan-windows-wsl2-and-cloudflare

This plan outlines the steps to set up a Llama LLM server on your Windows PC using Ubuntu Server via WSL2, integrate it with Cloudflare, connect it to your N8N instance, and establish a backup strategy.

**Goal:** Run a performant and accessible LLM server locally while coexisting with Windows and gaming, prioritising LLM performance.

**Hardware:** AMD Ryzen 7 5800x, RX 6800 XT 16GB, 32GB RAM (planning 128GB), 1TB NVMe SSD (planning more storage).

**Software:** Windows 11 (assuming, based on recent PCs), Ubuntu Server (via WSL2), Llama serving software (e.g., Ollama, llama.cpp), Cloudflare, N8N (on Google Cloud VM), Web Server (on Google Cloud VM), Docker (recommended).

### **Phase 1: Core Server Setup (Ubuntu Server on WSL2)**

1. **Install WSL2 and Ubuntu Server:**  
   * Ensure WSL2 is enabled on your Windows system. Open PowerShell as Administrator and run: `wsl --install -d Ubuntu-22.04` (or the latest LTS version). This will install WSL2 and the specified Ubuntu distribution.  
   * Follow the on-screen prompts to create a default Unix user account and password.  
   * Once installed, open the Ubuntu terminal from the Start Menu.

Update the package list and upgrade installed packages:  
sudo apt update  
sudo apt upgrade \-y

*   
2. **Basic Ubuntu Server Configuration:**

**Set Timezone:** Ensure the server's timezone is correct.  
sudo timedatectl set-timezone Europe/London \# Adjust to your preferred timezone

* 

**Create a dedicated user for services (Optional but Recommended):** For security, avoid running services directly as your main user or root.  
sudo adduser llm\_user  
sudo usermod \-aG sudo llm\_user \# Grant sudo privileges if needed for setup

* 

  Log out and back in as `llm_user` for subsequent service setup.  
3. **Secure the Ubuntu Server (WSL2 Context):**

**SSH:** By default, SSH might not be running or easily accessible from outside Windows in WSL2. You'll primarily access it via the Ubuntu terminal or `wsl.exe` from Windows. If you *do* need SSH access from other local network devices (less common with Cloudflare tunnel), configure and secure the SSH server:  
sudo apt install openssh-server  
sudo systemctl enable ssh \# Enable on boot (within WSL2 context)  
sudo systemctl start ssh

* 

  *Best Practice:* Disable password authentication for SSH and use key-based authentication. Edit `/etc/ssh/sshd_config`.

**Firewall (UFW):** While WSL2 networking can be complex and often relies on the Windows firewall, configuring UFW within Ubuntu is still good practice for internal WSL2 security or if you later expose ports differently.  
sudo apt install ufw  
sudo ufw enable  
sudo ufw default deny incoming  
sudo ufw default allow outgoing  
\# Allow SSH if you enabled it:  
sudo ufw allow ssh  
\# You will open ports for the LLM server later  
sudo ufw status verbose

*   
4. **Configure WSL2 for Automatic Startup:**  
   * WSL2 instances don't automatically start with Windows by default unless a WSL command is run. To ensure your Ubuntu instance is running for your 24/7 server, you can use a Windows Task Scheduler task or a simple script placed in your Windows Startup folder.  
   * **Using Task Scheduler (Recommended):**  
     * Open "Task Scheduler" in Windows.  
     * Create a Basic Task.  
     * Trigger: "When the computer starts".  
     * Action: "Start a program".  
     * Program/script: `wsl.exe`  
     * Add arguments: `-d Ubuntu-22.04 -e /usr/sbin/service ssh start` (This starts the specific distribution and runs a command inside it, like starting the SSH service, which keeps the instance alive. Adjust `Ubuntu-22.04` to your distribution name).  
     * Configure settings (e.g., run with highest privileges, run whether user is logged on or not \- be cautious with the latter for security).  
   * **Restart Schedule:** Your plan to restart the PC every 48 hours is a simple way to refresh the system. The Task Scheduler method will ensure the Ubuntu instance and services restart automatically after the Windows reboot.

### **Phase 2: Cloudflare Integration**

1. **Cloudflare Tunnel (Recommended Method):** Exposing services running on a dynamic home IP address directly is risky. Cloudflare Tunnel provides a secure way to connect your local service to Cloudflare's edge without opening firewall ports.  
   * Install `cloudflared` within your Ubuntu WSL2 instance: Follow the official Cloudflare documentation for installing `cloudflared` on Debian/Ubuntu.  
   * Authenticate `cloudflared` with your Cloudflare account.  
   * Create a Tunnel: `cloudflared tunnel create <tunnel-name>`  
   * Configure the Tunnel: Create a configuration file (e.g., `~/.cloudflared/config.yml`) mapping a subdomain (e.g., `llm.yourdomain.com`) to the local address and port where your LLM server will run (e.g., `http://localhost:5000`).  
   * Create DNS records in Cloudflare pointing your subdomain to the Tunnel.  
   * Run the Tunnel: `cloudflared tunnel run <tunnel-name>`. You'll want this to run automatically.  
2. **Automatic Tunnel Startup:**  
   * Create a systemd service for `cloudflared` within Ubuntu WSL2. This will ensure the tunnel starts whenever the WSL2 instance is running. The `cloudflared` documentation provides examples of systemd service files.  
3. **Cloudflare Access / Firewall Rules:**  
   * **Cloudflare Access:** Protect your LLM endpoint by requiring authentication or restricting access based on IP ranges. This is the most secure method. Configure an Access policy in the Cloudflare dashboard for your LLM subdomain, allowing only requests from your Google Cloud VM's static IP address (if it has one) or via other methods like service tokens.  
   * **Cloudflare Firewall Rules:** Alternatively, use Firewall Rules in Cloudflare to allow traffic to the LLM subdomain only from your Google Cloud VM's IP.  
4. **Connecting N8N to the LLM:**  
   * In your N8N workflows on the Google Cloud VM, configure the HTTP request or LLM node to point to your Cloudflare subdomain (e.g., `https://llm.yourdomain.com`).  
   * If using Cloudflare Access, your N8N instance will need to handle the authentication required by your Access policy (e.g., including an API key or token in the request headers).

### **Phase 3: Llama LLM Setup**

1. **Choose LLM Serving Software:**  
   * **Ollama:** Easy to install and use, good model management, built-in API. Excellent for getting started quickly. Has good AMD GPU support on Linux.  
   * **llama.cpp:** Highly performant, supports many models and quantisation methods. Requires compiling but offers great flexibility. The server component provides an API.  
   * **Text Generation Web UI:** A popular web interface that supports various backends (including llama.cpp, Ollama) and provides an OpenAI-compatible API endpoint.  
2. **Install and Configure LLM Software:**  
   * Install your chosen software within the Ubuntu WSL2 environment (as `llm_user` or another dedicated user). Follow the software's specific installation instructions.  
   * Download the desired LLM models. Consider quantised versions (like Q4 or Q8) to fit models into your 16GB GPU VRAM or offload layers to system RAM.  
   * Configure the software to listen on a specific port (e.g., 5000, 8000). Ensure it's configured to use your AMD GPU (ROCm).  
3. **Automatic LLM Server Startup:**  
   * Create a systemd service for your LLM serving software in Ubuntu WSL2. This ensures the server starts automatically when the WSL2 instance is running. The configuration will depend on the software you chose (e.g., `ollama serve`, `python -m llama_cpp.server`).

### **Phase 4: Google Cloud VM Web Server**

1. **Choose Web Server:**  
   * For a single-page personal website, a lightweight web server like **Caddy** or **Nginx** is sufficient and efficient.  
2. **Set up Web Server:**  
   * Install your chosen web server on the Google Cloud VM.  
   * Configure the web server to serve your static HTML/CSS/JS files for the personal website.  
   * Configure DNS records in Cloudflare to point your website subdomain (e.g., `www.yourdomain.com`) to the Google Cloud VM's public IP address. Cloudflare's proxying will provide CDN and SSL.  
3. **Coolify Consideration:**  
   * Coolify is a self-hosting platform that simplifies deploying applications using Docker. It requires Docker to be installed.  
   * Check the resource requirements for Coolify and compare them to the specifications of your free Google Cloud VM instance. Free tier VMs are often very limited in CPU, RAM, and disk space.  
   * **Recommendation:** Start with a simple web server (Nginx/Caddy) for your static site. If the VM resources prove sufficient and you have more complex deployment needs in the future, then explore installing Coolify.

### **Phase 5: Backup and Transferability**

1. **Docker for Services (Recommended):** Containerising your LLM server (Phase 3\) and N8N (already planned on Google Cloud) using Docker and Docker Compose greatly simplifies backup and transferability.  
   * Define your services (LLM server, N8N) in `docker-compose.yml` files.  
   * Use Docker volumes for persistent data (LLM models, configurations, N8N data).  
2. **Backup Strategy:**  
   * **Ubuntu Server (WSL2):**  
     * **WSL Distribution Export:** Regularly export your entire Ubuntu distribution state: `wsl --export Ubuntu-22.04 D:\backups\ubuntu_llm_backup_YYYYMMDD.tar`. Store this `.tar` file securely. This captures the OS, installed software, and configurations (except for Docker volumes).  
     * **Docker Volumes:** If using Docker, back up your Docker volumes. Tools like `docker run --rm -v llm_data:/data -v /path/to/backup:/backup ubuntu tar cvf /backup/llm_data_backup.tar /data` can be used.  
     * **Configuration Files:** Store your `docker-compose.yml` files, LLM software configuration, Cloudflare Tunnel config, and systemd service files in a version-controlled repository (e.g., **GitHub**).  
   * **Google Cloud VM:**  
     * **VM Snapshots:** Use Google Cloud's snapshot feature to create point-in-time backups of your VM's disk. Schedule these regularly.  
     * **Application Data:** Ensure N8N data (database, workflows) and website files are backed up. If N8N uses a separate database, back up that database. If N8N and the web server are in Docker, back up their volumes as described above.  
     * Store application-specific configuration files in **GitHub**.  
   * **LLM Models:** LLM model files are large. Store copies on a separate local drive or reliable cloud storage. They don't need to be part of the daily OS backup, but the specific model *files* you use should be protected.  
3. **Online and Offline Backup:**  
   * **Offline:** Store backups (WSL exports, volume backups, model copies) on an external hard drive kept physically separate from your PC.  
   * **Online:** Use cloud storage (Google Drive, Dropbox, S3-compatible storage) to store copies of your backups for off-site redundancy. Consider encrypted backups for privacy.  
4. **Transferability:**  
   * Using **Docker** makes services highly transferable. You can restore your Docker Compose setup and volumes on a different machine (Windows with Docker Desktop, Linux server) with minimal reconfiguration.  
   * Storing configuration and scripts in **GitHub** allows you to quickly set up services on a new machine by cloning your repository.  
   * **Docker Hub (or Private Registry):** If you build custom Docker images, pushing them to Docker Hub or a private registry makes deploying them elsewhere straightforward. For standard applications like N8N or common LLM images, you can just pull from public repositories.

### **Ongoing Considerations:**

* **Resource Monitoring:** Keep an eye on resource usage (CPU, RAM, GPU) on both Windows and within WSL2, especially when running MSFS2020 and the LLM server concurrently. Use Windows Task Manager and `htop`/`nvidia-smi` (or `roc-smi` if available and configured) in Ubuntu.  
* **Windows Updates:** Be mindful of Windows updates, as they can sometimes affect WSL2 or require restarts.  
* **LLM Software Updates:** Keep your LLM serving software and models updated for performance improvements and new features.  
* **Security:** Regularly review firewall rules, Cloudflare Access policies, and SSH configurations. Keep the Ubuntu system updated (`sudo apt update && sudo apt upgrade`).

This plan provides a solid framework. As you implement it, you'll gain practical experience and can adjust based on your specific performance observations and needs. Good luck with your setup\!

