# Podman Installation Guide

This guide provides detailed instructions for installing Podman as an alternative to Docker for running the n8n local setup.

## What is Podman?

Podman is a daemonless, open-source container engine that's fully compatible with Docker. Key advantages:
- **Rootless containers**: Enhanced security by default
- **No daemon required**: Direct container execution
- **Docker compatibility**: Drop-in replacement for most Docker commands
- **Pod support**: Native Kubernetes pod functionality

## 🐧 Linux (Ubuntu/Debian)

### Method 1: Official Repository (Recommended)
```bash
# Update package index
sudo apt update

# Install Podman and Podman Compose
sudo apt install -y podman podman-compose

# Verify installation
podman --version
podman-compose --version
```

### Method 2: Latest Version from Kubic Repository
```bash
# Add the repository
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_$(lsb_release -rs)/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list

# Add GPG key
curl -fsSL https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/xUbuntu_$(lsb_release -rs)/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/devel_kubic_libcontainers_stable.gpg > /dev/null

# Update and install
sudo apt update
sudo apt install -y podman podman-compose
```

### For Other Linux Distributions

**CentOS/RHEL/Fedora:**
```bash
# Fedora
sudo dnf install -y podman podman-compose

# CentOS/RHEL (with EPEL)
sudo yum install -y podman podman-compose
```

**Arch Linux:**
```bash
sudo pacman -S podman podman-compose
```

### Configure Docker Compatibility (Optional)
```bash
# Enable Podman socket for Docker API compatibility
systemctl --user enable --now podman.socket

# Create docker command aliases
echo 'alias docker=podman' >> ~/.bashrc
echo 'alias docker-compose=podman-compose' >> ~/.bashrc
source ~/.bashrc
```

## 🍎 macOS

### Method 1: Homebrew (Recommended)
```bash
# Install Homebrew if not already installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Podman
brew install podman

# Install Docker Compose (works with Podman)
brew install docker-compose

# Initialize and start Podman machine
podman machine init
podman machine start

# Verify installation
podman --version
docker-compose --version
```

### Method 2: Podman Desktop (GUI)
```bash
# Install Podman Desktop via Homebrew
brew install --cask podman-desktop

# Or download manually from: https://podman-desktop.io/downloads/macos
```

### Configure Docker Compatibility
```bash
# Enable Podman socket for Docker Compose compatibility
podman system service --time=0 unix:///tmp/podman-run-$(id -u)/podman/podman.sock &

# Set environment variable
export DOCKER_HOST=unix:///tmp/podman-run-$(id -u)/podman/podman.sock

# Add to shell profile for persistence
echo 'export DOCKER_HOST=unix:///tmp/podman-run-$(id -u)/podman/podman.sock' >> ~/.zshrc
echo 'alias docker=podman' >> ~/.zshrc
```

### Start Podman Socket Automatically
Add to your `~/.zshrc`:
```bash
# Auto-start Podman socket service
if ! pgrep -f "podman system service" > /dev/null; then
    podman system service --time=0 unix:///tmp/podman-run-$(id -u)/podman/podman.sock &
fi
```

## 🪟 Windows

### Method 1: Podman Desktop (Recommended)
1. **Download**: Visit [podman-desktop.io](https://podman-desktop.io/downloads/windows)
2. **Install**: Run the installer as Administrator
3. **Setup**: Launch Podman Desktop and initialize machine when prompted

### Method 2: Windows Subsystem for Linux (WSL2)
```powershell
# Enable WSL2 (run in PowerShell as Admin)
wsl --install

# Install Ubuntu from Microsoft Store
# Then follow Linux instructions inside WSL2
```

### Method 3: Manual Installation
1. **Download**: Get `podman-remote-release-windows_amd64.zip` from [GitHub releases](https://github.com/containers/podman/releases)
2. **Extract**: To `C:\Program Files\Podman`
3. **PATH**: Add to system PATH environment variable
4. **Initialize**:
   ```powershell
   podman machine init
   podman machine start
   ```

### Docker Compose on Windows
```powershell
# Install Docker Compose via Chocolatey
choco install docker-compose

# Or download from GitHub releases
# https://github.com/docker/compose/releases
```

## ✅ Verification

Test your installation:

```bash
# Check Podman version
podman --version

# Test container functionality
podman run --rm hello-world

# Check Docker Compose compatibility
docker-compose --version

# Test with a simple compose file
echo "version: '3'
services:
  test:
    image: alpine
    command: echo 'Podman works!'" > test-compose.yml

docker-compose -f test-compose.yml up
rm test-compose.yml
```

## 🔧 Using with n8n-local

Once Podman is installed and configured, you can use it with the n8n setup:

### Option 1: Direct Podman Commands
```bash
cd n8n-local

# If podman-compose is available
podman-compose up -d
podman-compose ps
podman-compose logs -f n8n
podman-compose down
```

### Option 2: Docker Compose with Podman (Recommended)
```bash
cd n8n-local

# Use docker-compose (which will use Podman backend)
docker-compose up -d
docker-compose ps
docker-compose logs -f n8n
docker-compose down
```

## 🛠️ Troubleshooting

### Common Issues

**"Connection refused" errors:**
```bash
# Restart Podman machine (macOS/Windows)
podman machine stop
podman machine start

# Or restart socket service (Linux)
systemctl --user restart podman.socket
```

**Permission denied (Linux):**
```bash
# Add user to podman group
sudo usermod -aG podman $USER
newgrp podman
```

**Port conflicts:**
```bash
# Check for running containers
podman ps -a

# Stop all containers
podman stop $(podman ps -aq)
```

**Docker Compose not finding Podman:**
```bash
# Verify DOCKER_HOST is set
echo $DOCKER_HOST

# Set it manually if needed
export DOCKER_HOST=unix:///tmp/podman-run-$(id -u)/podman/podman.sock
```

## 📚 Additional Resources

- [Podman Official Documentation](https://docs.podman.io/)
- [Podman Desktop](https://podman-desktop.io/)
- [Podman vs Docker Comparison](https://docs.podman.io/en/latest/markdown/podman.1.html#comparison-with-docker)
- [Podman Compose Documentation](https://github.com/containers/podman-compose)

## 🔒 Security Benefits

Podman provides enhanced security features:
- **Rootless containers**: Containers run with user privileges
- **No daemon**: Eliminates daemon-related attack vectors
- **SELinux support**: Better integration with security frameworks
- **User namespaces**: Isolated container user spaces