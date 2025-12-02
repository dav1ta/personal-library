# PostgreSQL Installation Guide

This guide covers the installation and initial setup of PostgreSQL across different operating systems.

## Table of Contents
- [System Requirements](#system-requirements)
- [Installation Methods](#installation-methods)
- [Post-Installation Steps](#post-installation-steps)
- [Troubleshooting](#troubleshooting)

## System Requirements

### Minimum Requirements
- CPU: 1 GHz or faster
- RAM: 1 GB minimum (4 GB recommended)
- Storage: 10 GB minimum free space
- Operating System: Linux, macOS, or Windows

### Recommended Requirements
- CPU: 2 GHz or faster
- RAM: 8 GB or more
- Storage: 50 GB or more free space
- SSD storage for better performance

## Installation Methods

### Ubuntu/Debian
```bash
# Update package lists
sudo apt update

# Install PostgreSQL and contrib package
sudo apt install postgresql postgresql-contrib

# Verify installation
psql --version
```

### macOS (using Homebrew)
```bash
# Update Homebrew
brew update

# Install PostgreSQL
brew install postgresql

# Start PostgreSQL service
brew services start postgresql

# Verify installation
psql --version
```

### Windows
1. Download the installer from [PostgreSQL Downloads](https://www.postgresql.org/download/windows/)
2. Run the installer and follow the wizard
3. Choose components to install (PostgreSQL Server, pgAdmin, Command Line Tools)
4. Set password for the postgres user
5. Choose port (default: 5432)
6. Complete the installation

### Docker
```bash
# Pull the official PostgreSQL image
docker pull postgres

# Run PostgreSQL container
docker run --name my-postgres \
    -e POSTGRES_PASSWORD=mysecretpassword \
    -p 5432:5432 \
    -d postgres
```

## Post-Installation Steps

### 1. Verify Installation
```bash
# Check PostgreSQL version
psql --version

# Connect to PostgreSQL
psql -U postgres
```

### 2. Basic Security Setup
```sql
-- Change postgres user password
ALTER USER postgres WITH PASSWORD 'new_password';

-- Create a new user
CREATE USER myuser WITH PASSWORD 'mypassword';

-- Create a new database
CREATE DATABASE mydatabase;

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE mydatabase TO myuser;
```

### 3. Enable Remote Access (Optional)
Edit `postgresql.conf`:
```conf
listen_addresses = '*'  # or specific IP
```

Edit `pg_hba.conf`:
```conf
# Allow remote connections
host    all             all             0.0.0.0/0               md5
```

## Troubleshooting

### Common Issues

1. **Connection Refused**
   - Check if PostgreSQL is running
   - Verify port configuration
   - Check firewall settings

2. **Authentication Failed**
   - Verify username and password
   - Check pg_hba.conf configuration
   - Ensure proper permissions

3. **Port Already in Use**
   - Check if another PostgreSQL instance is running
   - Change port in postgresql.conf
   - Kill the process using the port

### Getting Help

- [PostgreSQL Official Documentation](https://www.postgresql.org/docs/)
- [Stack Overflow PostgreSQL Tag](https://stackoverflow.com/questions/tagged/postgresql)
- [PostgreSQL Mailing Lists](https://www.postgresql.org/community/lists/)

## Next Steps

- [Basic Configuration](configuration.md)
- Basic Operations
- Security Best Practices 