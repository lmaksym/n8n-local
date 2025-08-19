# n8n Local Setup

This repository provides a Docker Compose configuration for running [n8n](https://n8n.io/) workflow automation tool locally with PostgreSQL database.

## 📋 Prerequisites

Before you begin, ensure you have the following installed on your system:

### Required Software
- **Docker**: Install Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop/)
- **Docker Compose**: Usually included with Docker Desktop (for standalone installation, see [Docker Compose docs](https://docs.docker.com/compose/install/))
- **Git** (optional): For cloning the repository

### System Requirements
- **RAM**: Minimum 2GB available memory
- **Storage**: At least 1GB free disk space
- **Operating System**: Windows 10+, macOS 10.15+, or Linux

## 🚀 Quick Start

### Option 1: Clone with Git

```bash
# Clone the repository
git clone https://github.com/lmaksym/n8n-local.git

# Navigate to the project directory
cd n8n-local
```

### Option 2: Download as Archive

1. Download the repository as a ZIP file from [GitHub](https://github.com/lmaksym/n8n-local/archive/refs/heads/main.zip)
2. Extract the ZIP file to your desired location
3. Navigate to the extracted folder

## ⚙️ Configuration

### Environment Setup

The Docker Compose file is pre-configured with the following defaults:

- **n8n Web Interface**: `http://localhost:5678`
- **Database**: PostgreSQL 15
- **Timezone**: `Europe/Warsaw` (adjust in docker-compose.yml if needed)
- **Data Persistence**: Local volumes for both n8n and PostgreSQL data

### Security Configuration

⚠️ **Important**: Before running in production, change the default PostgreSQL password:

1. Open `docker-compose.yml`
2. Locate the line: `- POSTGRES_PASSWORD=n8n`
3. Replace `n8n` with a strong password
4. Update the corresponding `DB_POSTGRESQL_PASSWORD=n8n` line in the n8n service

## 🏃‍♂️ Running n8n

### Start the Services

```bash
# Start n8n and PostgreSQL in detached mode
docker-compose up -d
```

### Verify Installation

```bash
# Check if containers are running
docker-compose ps

# View logs (optional)
docker-compose logs -f n8n
```

### Access n8n

1. Open your web browser
2. Navigate to `http://localhost:5678`
3. Complete the initial setup wizard
4. Start creating your workflows!

## 🛠️ Management Commands

### Essential Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View running containers
docker-compose ps

# View logs
docker-compose logs n8n          # n8n logs only
docker-compose logs postgres     # PostgreSQL logs only
docker-compose logs -f           # Follow all logs

# Restart services
docker-compose restart

# Update to latest n8n version
docker-compose pull
docker-compose up -d
```

### Data Management

```bash
# Backup your n8n data
docker-compose exec postgres pg_dump -U n8n n8n > n8n_backup.sql

# Stop services (keeps data)
docker-compose down

# Remove everything including data (⚠️ DESTRUCTIVE)
docker-compose down -v
```

## 📁 Data Persistence

Your data is automatically persisted in local directories:

- `./n8n_data/` - n8n workflows, credentials, and settings
- `./postgres_data/` - PostgreSQL database files

These directories are created automatically and are excluded from Git via `.gitignore`.

## 🔧 Troubleshooting

### Common Issues

**Port 5678 already in use:**
```bash
# Check what's using the port
lsof -i :5678  # macOS/Linux
netstat -ano | findstr :5678  # Windows

# Either stop the conflicting service or change the port in docker-compose.yml
```

**Container won't start:**
```bash
# Check container logs
docker-compose logs n8n

# Ensure Docker is running
docker --version
```

**Database connection issues:**
```bash
# Restart services in correct order
docker-compose down
docker-compose up -d postgres
# Wait 10 seconds
docker-compose up -d n8n
```

**Permission issues (Linux):**
```bash
# Fix ownership of data directories
sudo chown -R $USER:$USER ./n8n_data ./postgres_data
```

### Reset Installation

To start fresh with a clean installation:

```bash
# Stop services and remove all data
docker-compose down -v

# Remove local data directories
rm -rf ./n8n_data ./postgres_data

# Start services again
docker-compose up -d
```

## 🎯 Next Steps

After successful installation:

1. **Explore n8n**: Check out the [n8n documentation](https://docs.n8n.io/)
2. **Create workflows**: Start with simple automation tasks
3. **Configure credentials**: Set up connections to your favorite services
4. **Schedule workflows**: Use n8n's built-in scheduler for recurring tasks

## 📚 Additional Resources

- [n8n Official Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

## ⚠️ Security Notes

- This setup is intended for local development and testing
- For production use, implement proper security measures:
  - Use strong passwords
  - Enable SSL/TLS
  - Configure firewall rules
  - Regular security updates

## 📄 License

This project is open source. Please refer to n8n's [license](https://github.com/n8n-io/n8n/blob/master/LICENSE.md) for usage terms.