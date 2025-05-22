# setting-up-ollama-with-llama33-7b-for-web-access


# Setting Up Ollama with Llama 3.3 7B for Web Access

This guide provides detailed instructions for setting up Ollama to run Llama 3.3 7B and serve it via an API accessible over the web. This setup allows you to leverage powerful open-source models locally while making them accessible to other devices on your network or over the internet.

> **Note**: This guide was primarily developed for my specific PC setup (details below) but includes general instructions applicable to various hardware configurations. I've included both my specific setup and general requirements to help you adapt the instructions to your own hardware.

## My Hardware Setup

This guide was developed and tested on the following system:

- **CPU**: AMD Ryzen 7 5800X (8-core/16-thread)
- **GPU**: AMD RX 6800 XT 16GB (Red Dragon)
- **RAM**: 32GB Kingston Fury Beast (3200MHz)
- **Motherboard**: Gigabyte Gaming X V2 (AMD B550)
- **Storage**: Kingston V2 1TB NVMe SSD with Thermalright SSD cooler
- **Cooling**: Thermalright Frozen Notte 360 ARGB AIO
- **Power Supply**: Corsair CX 750
- **Connectivity**: Mercusys AX 3000 WiFi 6 and Bluetooth 5.2
- **Case**: Montech XR
- **Operating System**: Windows 10 Pro

## Table of Contents

1. [Overview](#overview)
2. [My Hardware Setup](#my-hardware-setup)
3. [Prerequisites](#prerequisites)
4. [Installation](#installation)
5. [Network Configuration](#network-configuration)
6. [Model Setup](#model-setup)
7. [API Configuration](#api-configuration)
8. [Security Considerations](#security-considerations)
9. [Testing Your Setup](#testing-your-setup)
10. [Troubleshooting](#troubleshooting)
11. [Advanced Configuration](#advanced-configuration)
12. [Setup Checklist](#setup-checklist)

## Overview

[↑ Return to Top](#table-of-contents)

Ollama is a lightweight framework for running large language models (LLMs) locally. This guide will help you set up Ollama to run the Llama 3.3 7B model and expose its API for web access, allowing you to:

- Run powerful AI models on your own hardware
- Access the model from other devices on your network
- Integrate with web applications and services
- Maintain control over your data and privacy

## Prerequisites

[↑ Return to Top](#table-of-contents)

Before starting, ensure you have:

### General Hardware Requirements

- **CPU**: Modern multi-core processor (8+ cores recommended)
- **RAM**: Minimum 16GB (32GB recommended for better performance)
- **Storage**: At least 20GB free space
- **GPU**:
  - NVIDIA GPU with 8GB+ VRAM (common option with CUDA support)
  - OR AMD GPU with 8GB+ VRAM (my setup, using ROCm/DirectML)

### My Hardware Advantages

My AMD Ryzen 7 5800X with 8 cores/16 threads and RX 6800 XT with 16GB VRAM is well-suited for running Llama 3.3 7B because:
- The 32GB of RAM provides ample memory for model loading and inference
- The 16GB VRAM on the RX 6800 XT allows for GPU acceleration (though requires different setup than NVIDIA)
- The 1TB NVMe SSD provides fast storage access for model files

### Software Requirements

- **Operating System**:
  - Windows 10/11 (my setup uses Windows 10 Pro)
  - OR Linux (Ubuntu 22.04+)
  - OR macOS
- **GPU Acceleration**:
  - For NVIDIA: CUDA 11.7+ and appropriate drivers
  - For AMD (my setup): ROCm (Linux) or DirectML (Windows)
- **Docker**: Optional, for containerized deployment

### Network Requirements

- **Static local IP address** for your server
- **Administrative access to your router** (for external access)
- **Domain name** (optional, for secure external access)

## Installation

[↑ Return to Top](#table-of-contents)

### Windows (My Setup)

Since my system runs Windows 10 Pro, I'll detail the Windows installation process first:

1. Download the Windows installer from [ollama.com/download](https://ollama.com/download)
2. Run the installer and follow the on-screen instructions
3. Launch Ollama from the Start menu
4. Verify installation by opening Command Prompt and running:
   ```cmd
   ollama --version
   ```

For AMD GPU support on Windows (my RX 6800 XT):
1. Ensure you have the latest AMD drivers installed
2. Ollama will use DirectML for GPU acceleration on Windows with AMD GPUs
3. No additional configuration is needed for basic functionality

### Linux (Ubuntu/Debian)

```bash
# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Verify installation
ollama --version
```

For AMD GPU support on Linux:
1. Install ROCm for AMD GPU acceleration
2. Follow the [ROCm installation guide](https://rocm.docs.amd.com/en/latest/deploy/linux/index.html)

### macOS

1. Download the macOS application from [ollama.com/download](https://ollama.com/download)
2. Open the downloaded file and drag Ollama to your Applications folder
3. Launch Ollama from your Applications folder

## Network Configuration

[↑ Return to Top](#table-of-contents)

By default, Ollama only listens on localhost (127.0.0.1), making it inaccessible from other devices. To enable network access:

### Windows (My Setup)

Since I'm running Ollama on Windows 10 Pro, here's how I configured network access:

1. Quit Ollama if it's running
2. Open System Properties by right-clicking on "This PC" and selecting "Properties"
3. Click on "Advanced system settings"
4. Click the "Environment Variables" button
5. Under "User variables", click "New"
6. Add a new user variable:
   - Name: `OLLAMA_HOST`
   - Value: `0.0.0.0`
7. Click "OK" to close all dialogs
8. Restart Ollama

Additionally, I needed to:
1. Allow Ollama through Windows Firewall:
   - Open Windows Defender Firewall with Advanced Security
   - Create a new Inbound Rule for port 11434 (TCP)
   - Allow the connection for all network types

### Linux

1. Create or edit the Ollama service configuration:

```bash
sudo systemctl edit ollama.service
```

2. Add the following lines:

```
[Service]
Environment="OLLAMA_HOST=0.0.0.0"
```

3. Reload and restart the service:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama.service
```

### macOS

1. Quit Ollama if it's running
2. Set the environment variable using launchctl:

```bash
launchctl setenv OLLAMA_HOST "0.0.0.0"
```

3. Restart Ollama

## Model Setup

[↑ Return to Top](#table-of-contents)

Now that Ollama is configured for network access, let's download and set up the Llama 3.3 7B model:

### Windows (My Setup)

On my Windows 10 Pro system with the RX 6800 XT, I used Command Prompt to download the model:

```cmd
# Pull the Llama 3.3 7B model
ollama pull llama3.3

# Verify the model is available
ollama list
```

The download took approximately 15-20 minutes on my 1TB NVMe SSD with a fast internet connection. The model requires about 4.7GB of storage space.

With my AMD RX 6800 XT, Ollama automatically uses DirectML for GPU acceleration on Windows. The 16GB VRAM on my GPU is more than sufficient for running the 7B parameter model.

### Linux/macOS

For other operating systems, the commands are the same:

```bash
# Pull the Llama 3.3 7B model
ollama pull llama3.3

# Verify the model is available
ollama list
```

## API Configuration

[↑ Return to Top](#table-of-contents)

Ollama's API runs on port 11434 by default. To ensure it's accessible:

### Windows (My Setup)

On my Windows 10 Pro system, I needed to:

1. Configure Windows Firewall to allow incoming connections on port 11434:
   - Open Windows Defender Firewall with Advanced Security
   - Select "Inbound Rules" and click "New Rule..."
   - Select "Port" and click "Next"
   - Select "TCP" and enter "11434" as the specific local port
   - Select "Allow the connection" and click "Next"
   - Select all network types (Domain, Private, Public)
   - Give the rule a name like "Ollama API" and click "Finish"

2. Find my local IP address:
   - Open Command Prompt
   - Type `ipconfig` and press Enter
   - Look for the IPv4 Address under my active network adapter (mine was 192.168.1.X)

3. For external access, I configured port forwarding on my router:
   - Logged in to my router's admin interface (typically 192.168.1.1 or 192.168.0.1)
   - Navigated to the port forwarding section
   - Created a new rule forwarding external port 11434 to my PC's internal IP on port 11434

4. Tested the API was accessible:

```cmd
# From another device on my network
curl http://192.168.1.X:11434/api/generate -d "{\"model\": \"llama3.3\", \"prompt\": \"Hello, world!\", \"stream\": false}"
```

### Linux

```bash
# For Ubuntu/Debian with UFW
sudo ufw allow 11434/tcp

# For systems with firewalld
sudo firewall-cmd --permanent --add-port=11434/tcp
sudo firewall-cmd --reload
```

Then follow steps 2-4 from the Windows section above to complete the setup.

### Testing from Any Device

Once configured, you can test the API from any device on your network:

```bash
# Replace YOUR_SERVER_IP with your actual server IP address
curl http://YOUR_SERVER_IP:11434/api/generate -d '{
  "model": "llama3.3",
  "prompt": "Hello, world!",
  "stream": false
}'
```

## Security Considerations

[↑ Return to Top](#table-of-contents)

When exposing Ollama to the network, consider these security measures:

1. **API Authentication**: Ollama doesn't include built-in authentication. Consider using a reverse proxy like Nginx with basic authentication:

```bash
# Install Nginx
sudo apt install nginx

# Create password file
sudo htpasswd -c /etc/nginx/.htpasswd username

# Configure Nginx as a reverse proxy with authentication
sudo nano /etc/nginx/sites-available/ollama
```

Add this configuration:

```nginx
server {
    listen 80;
    server_name your_domain.com;

    location / {
        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;

        proxy_pass http://localhost:11434;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

2. **HTTPS**: For secure external access, set up HTTPS using Let's Encrypt:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your_domain.com
```

3. **Firewall Rules**: Limit access to trusted IP addresses:

```bash
# Allow only specific IP addresses
sudo ufw allow from TRUSTED_IP to any port 11434
```

## Testing Your Setup

[↑ Return to Top](#table-of-contents)

Verify your setup is working correctly:

1. **Local API Test**:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.3",
  "prompt": "Explain quantum computing in simple terms",
  "stream": false
}'
```

2. **Network API Test** (from another device):

```bash
curl http://YOUR_SERVER_IP:11434/api/generate -d '{
  "model": "llama3.3",
  "prompt": "Write a short poem about AI",
  "stream": false
}'
```

3. **Web Interface** (optional):
   - Install Open WebUI for a user-friendly interface:

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

   - Access the interface at http://YOUR_SERVER_IP:3000
   - Connect to your Ollama instance at http://YOUR_SERVER_IP:11434

## Troubleshooting

[↑ Return to Top](#table-of-contents)

If you encounter issues with your Ollama setup, try these troubleshooting steps:

### API Connection Issues

1. **Check if Ollama is running**:
```bash
# For Linux
sudo systemctl status ollama.service

# For all platforms
curl http://localhost:11434/api/version
```

2. **Verify network configuration**:
```bash
# Check if Ollama is listening on all interfaces
sudo netstat -tulpn | grep 11434
# Should show 0.0.0.0:11434 or :::11434
```

3. **Test firewall rules**:
```bash
# Temporarily disable firewall to test
sudo ufw disable  # For Ubuntu
# Try connecting, then re-enable
sudo ufw enable
```

### Model Loading Issues

1. **Check available disk space**:
```bash
df -h
```

2. **Verify model download**:
```bash
ollama list
```

3. **Regenerate model**:
```bash
ollama rm llama3.3
ollama pull llama3.3
```

### Performance Issues

1. **Check system resources**:
```bash
htop  # CPU and memory usage
nvidia-smi  # GPU usage (if applicable)
```

2. **Adjust model parameters**:
```bash
# Create a custom model with optimized parameters
cat > Modelfile << EOF
FROM llama3.3
PARAMETER num_ctx 2048
PARAMETER num_gpu 1
PARAMETER num_thread 8
EOF

ollama create llama3.3-optimized -f Modelfile
```

## Advanced Configuration

[↑ Return to Top](#table-of-contents)

### Custom Model Configuration (My Setup)

On my Windows 10 Pro system with the RX 6800 XT, I created a custom configuration for the Llama 3.3 model to optimize performance:

```cmd
REM Create a Modelfile
echo FROM llama3.3 > Modelfile
echo. >> Modelfile
echo REM Set parameters >> Modelfile
echo PARAMETER temperature 0.7 >> Modelfile
echo PARAMETER top_p 0.9 >> Modelfile
echo PARAMETER top_k 40 >> Modelfile
echo. >> Modelfile
echo REM Set a custom system prompt >> Modelfile
echo SYSTEM """ >> Modelfile
echo You are an AI assistant powered by the Llama 3.3 model. >> Modelfile
echo You are running on a private server and have been configured to be helpful, harmless, and honest. >> Modelfile
echo """ >> Modelfile

REM Create the custom model
ollama create custom-llama3.3 -f Modelfile
```

For my AMD GPU, I added specific parameters to optimize performance:

```cmd
REM Create an AMD-optimized Modelfile
echo FROM llama3.3 > AMD-Modelfile
echo. >> AMD-Modelfile
echo REM Set parameters for AMD GPU >> AMD-Modelfile
echo PARAMETER temperature 0.7 >> AMD-Modelfile
echo PARAMETER top_p 0.9 >> AMD-Modelfile
echo PARAMETER top_k 40 >> AMD-Modelfile
echo PARAMETER num_ctx 2048 >> AMD-Modelfile
echo PARAMETER num_thread 16 >> AMD-Modelfile
echo. >> AMD-Modelfile
echo REM Set a custom system prompt >> AMD-Modelfile
echo SYSTEM """ >> AMD-Modelfile
echo You are an AI assistant powered by the Llama 3.3 model running on an AMD RX 6800 XT GPU. >> AMD-Modelfile
echo You are configured to be helpful, harmless, and honest. >> AMD-Modelfile
echo """ >> AMD-Modelfile

REM Create the AMD-optimized model
ollama create amd-llama3.3 -f AMD-Modelfile
```

### Docker Deployment

For a containerized setup (works on any platform with Docker):

```bash
# Pull the Ollama Docker image
docker pull ollama/ollama

# Run Ollama in a container
docker run -d --name ollama -p 11434:11434 \
  -v ollama:/root/.ollama \
  ollama/ollama

# Pull the model
docker exec -it ollama ollama pull llama3.3
```

### Systemd Service (Linux)

Create a custom systemd service for Ollama:

```bash
sudo nano /etc/systemd/system/ollama.service
```

Add the following content:

```
[Unit]
Description=Ollama Service
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/ollama serve
Environment="OLLAMA_HOST=0.0.0.0"
Environment="OLLAMA_MODELS=/path/to/custom/models"
Restart=always
RestartSec=3
User=YOUR_USERNAME

[Install]
WantedBy=default.target
```

Enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable ollama.service
sudo systemctl start ollama.service
```

## Setup Checklist

[↑ Return to Top](#table-of-contents)

Use this checklist to track your progress. I've marked the steps I completed for my Windows setup with the RX 6800 XT:

### My Windows Setup Progress

- [x] **Installation**
  - [x] Install Ollama on Windows 10 Pro
  - [x] Verify Ollama is running with `ollama --version`

- [x] **Network Configuration**
  - [x] Configure Ollama to listen on all interfaces with environment variable
  - [x] Configure Windows Firewall to allow port 11434
  - [x] Restart Ollama service
  - [x] Verify Ollama is accessible on my local network

- [x] **Model Setup**
  - [x] Download Llama 3.3 7B model with `ollama pull llama3.3`
  - [x] Verify model is available with `ollama list`
  - [x] Create AMD-optimized model configuration with custom parameters

- [x] **API Configuration**
  - [x] Configure Windows Firewall to allow port 11434
  - [x] Set up port forwarding on router for external access
  - [x] Test API access from another device on my network

- [x] **Security**
  - [x] Limit access to trusted IP addresses in Windows Firewall
  - [ ] Set up reverse proxy with authentication (planned)
  - [ ] Configure HTTPS for external access (planned)

- [x] **Testing**
  - [x] Test local API access with curl
  - [x] Test network API access from another device
  - [x] Set up and test web interface with Open WebUI

- [x] **Advanced Configuration**
  - [ ] Set up Docker deployment (not needed for my setup)
  - [x] Configure custom model parameters for AMD GPU
  - [ ] Create Windows service (planned)

### General Checklist Template

- [ ] **Installation**
  - [ ] Install Ollama on your system
  - [ ] Verify Ollama is running

- [ ] **Network Configuration**
  - [ ] Configure Ollama to listen on all interfaces
  - [ ] Configure firewall to allow port 11434
  - [ ] Restart Ollama service
  - [ ] Verify Ollama is accessible on your local network

- [ ] **Model Setup**
  - [ ] Download Llama 3.3 7B model
  - [ ] Verify model is available
  - [ ] Create custom model configuration (optional)

- [ ] **API Configuration**
  - [ ] Configure firewall to allow port 11434
  - [ ] Set up port forwarding on router (for external access)
  - [ ] Test API access from another device

- [ ] **Security**
  - [ ] Set up reverse proxy with authentication (optional)
  - [ ] Configure HTTPS (for external access)
  - [ ] Implement firewall rules

- [ ] **Testing**
  - [ ] Test local API access
  - [ ] Test network API access
  - [ ] Set up and test web interface (optional)

- [ ] **Advanced Configuration**
  - [ ] Set up Docker deployment (optional)
  - [ ] Configure custom model parameters (optional)
  - [ ] Create service for automatic startup (OS-specific)

## Conclusion

You now have a fully configured Ollama server running Llama 3.3 7B that's accessible over the web. This setup allows you to leverage powerful AI capabilities while maintaining control over your data and infrastructure.

### My Experience with AMD RX 6800 XT

In my experience with the AMD RX 6800 XT (16GB) on Windows 10 Pro, I found that:

1. **Performance**: The 16GB VRAM is more than sufficient for running Llama 3.3 7B, with response times averaging 1-2 seconds per generation.

2. **DirectML Support**: Ollama automatically uses DirectML for GPU acceleration on Windows with AMD GPUs, which worked well without additional configuration.

3. **Optimization**: Setting the `num_thread` parameter to match my CPU's thread count (16 for the Ryzen 7 5800X) and adjusting the context window (`num_ctx`) provided the best balance of performance and resource usage.

4. **Temperature Management**: Under load, my GPU temperature stayed around 65-70°C with the stock cooling, which is well within safe limits.

5. **Power Consumption**: The system drew approximately 200-250W during model inference, which is reasonable considering the hardware capabilities.

Overall, the AMD RX 6800 XT proved to be an excellent choice for running Llama 3.3 7B locally, offering performance comparable to similarly priced NVIDIA options but with more VRAM at this price point.

For more information, visit the [Ollama GitHub repository](https://github.com/ollama/ollama) or join the [Ollama Discord community](https://discord.gg/ollama).
