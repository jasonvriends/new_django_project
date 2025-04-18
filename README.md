# Django Project Setup Guide

## Table of Contents
- [Prerequisites](#prerequisites)
- [Setup Development Environment](#setup-development-environment)
  - [Install Windows Subsystem for Linux (WSL)](#install-windows-subsystem-for-linux-wsl)
  - [Configure PostgreSQL](#configure-postgresql)
- [IDE Setup](#ide-setup)
  - [Install and Configure Cursor AI](#install-and-configure-cursor-ai)
- [Django Project Creation](#django-project-creation)
  - [Generate Project with Cookiecutter](#generate-project-with-cookiecutter)
  - [Configure Python Environment](#configure-python-environment)
  - [Database Setup](#database-setup)
  - [Run Development Server](#run-development-server)
- [Next Steps](#next-steps)
- [Resources](#resources)

## Prerequisites

- **Operating System**: Windows 10 version 2004+ (Build 19041+) or Windows 11
- **Architecture**: 64-bit Windows (32-bit not supported)
- **Admin Access**: Administrator privileges for PowerShell/Command Prompt
- **Virtualization**: Enabled in BIOS/UEFI (verify in Task Manager > Performance > Virtualization)
- **Storage**: Minimum 5 GB free disk space for Linux distribution

## Setup Development Environment

### Install Windows Subsystem for Linux (WSL)

1. **Open PowerShell as Administrator**
   ```powershell
   # Press Win + S, type PowerShell, right-click, select "Run as administrator"
   ```

2. **Install WSL with Ubuntu**
   ```powershell
   wsl --install
   ```
   This command:
   - Enables WSL and Virtual Machine Platform features
   - Downloads and installs the WSL2 Linux kernel
   - Installs Ubuntu as the default distribution
   - Sets WSL2 as the default version

3. **Restart your computer when prompted**

4. **Complete Ubuntu Setup**
   - After restart, the Ubuntu installation will finalize
   - Create a username (lowercase recommended)
   - Set a password (characters won't display while typing)

5. **Verify Installation**
   ```bash
   # Update packages
   sudo apt update
   sudo apt upgrade -y
   
   # Install development packages
   sudo apt install git libpq-dev python3-dev build-essential postgresql pre-commit postgresql-contrib cookiecutter python3.12 python3.12-venv npm -y
   
   # Configure Git
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

6. **Verify WSL Version (in PowerShell)**
   ```powershell
   wsl -l -v
   ```

### Configure PostgreSQL

1. **Start and Enable PostgreSQL Service**
   ```bash
   sudo systemctl start postgresql.service
   sudo systemctl enable postgresql.service
   ```

2. **Configure Passwordless Connections (Development Only)**
   ```bash
   # Edit PostgreSQL authentication configuration
   sudo nano /etc/postgresql/$(pg_lsclusters | grep online | awk '{print $1}')/main/pg_hba.conf
   ```

3. **Modify Authentication Settings**
   ```
   # TYPE  DATABASE        USER            ADDRESS                 METHOD
   host    all             all             127.0.0.1/32            trust
   ```

4. **Restart PostgreSQL**
   ```bash
   sudo service postgresql restart
   ```

### Install Mailpit

1. **Download and Install Mailpit**
   ```bash
   # Install via the official script
   sudo bash < <(curl -sL https://raw.githubusercontent.com/axllent/mailpit/develop/install.sh)
   ```
2. **Run Mailpit**
   ```bash
   # Start Mailpit in a separate terminal
   ./mailpit
   # OR if using the system installation
   mailpit
   ```

4. **Access the Web Interface**
   - Open [http://127.0.0.1:8025/](http://127.0.0.1:8025/) in your browser
   - Mailpit will capture and display all emails sent by your Django application

## IDE Setup

### Install and Configure Cursor AI

1. **Download and Install**
   - Visit [https://cursor.com](https://cursor.com)
   - Download Windows installer
   - Run the installer and follow prompts
   - Alternative: Install via Windows Package Manager
     ```powershell
     winget install Cursor
     ```

2. **Launch Cursor AI**
   - Open from Start menu or desktop shortcut
   - On first launch:
     - Sign in or create an account
     - Select "Start from scratch" to avoid WSL issues

3. **Install WSL Extension**
   - Press `Ctrl+Shift+X` to open Extensions
   - Search for "WSL"
   - Install Microsoft's WSL extension
   - Reload if prompted

4. **Connect to WSL**
   - Click Remote Status Bar icon (bottom-left)
   - Select "Connect to WSL"
   - Choose your Ubuntu distribution
   - Wait for VS Code Server installation

5. **Access WSL Projects**
   ```bash
   # Clone repository (if using existing project)
   git clone https://github.com/<username>/<repository>.git
   cd <repository>
   cursor .
   ```

## Django Project Creation

### Generate Project with Cookiecutter

```bash
cookiecutter gh:cookiecutter/cookiecutter-django
```

> **Note**: You'll be prompted to configure project settings. See [Project Generation Options](https://cookiecutter-django.readthedocs.io/en/latest/1-getting-started/project-generation-options.html#template-options) for details.

### Configure Python Environment

```bash
# Navigate to project directory
cd <project_slug>

# Create and activate virtual environment
python3.12 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements/local.txt

# Set up pre-commit hooks
pre-commit install
```

### Database Setup

```bash
# Connect to PostgreSQL as postgres user
sudo -i -u postgres

# Create a new database
createdb --username=postgres <project_slug>

# Exit postgres user session
exit

# Configure database connection
export DATABASE_URL=postgres://postgres:@127.0.0.1:5432/<project_slug>

# Initialize database schema
python manage.py migrate
```

### Run Development Server

**Option 1: Synchronous**
```bash
python manage.py runserver 0.0.0.0:8000
```

**Option 2: Asynchronous**
```bash
uvicorn config.asgi:application --host 0.0.0.0 --reload --reload-include '*.html'
```

## Next Steps

After completing setup, you can:
- Create Django apps
- Define models
- Write views and templates
- Configure URLs
- Add tests

## Resources

- [Cookiecutter Django Documentation](https://cookiecutter-django.readthedocs.io/)
- [Official Django Documentation](https://docs.djangoproject.com/)
- [WSL Documentation](https://docs.microsoft.com/en-us/windows/wsl/)
- [Cursor AI Documentation](https://cursor.com/docs)

