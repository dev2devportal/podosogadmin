# PoDoSoGoAdmin

Created by Hawke Robinson
hawke at dev2dev dot net

This is under the AGPL3.0 License for the public.

A modern web and CLI administration tool for managing SOGo email users with Postfix and Dovecot with a PostGreSQl Database and virtuall domains, users, and alises, with full integration.

## ✅ Status: FULLY OPERATIONAL (as of 2025-08-13)
**User creation and management features are complete and tested in production.**
- ✅ Users can login to SOGo webmail
- ✅ Send and receive emails with external servers (Gmail tested)
- ✅ Complete bidirectional mail flow verified

## ⚠️ CRITICAL SECURITY NOTICE

### Network Security Requirements
**THIS APPLICATION IS NOT DESIGNED FOR PUBLIC INTERNET EXPOSURE**

This platform MUST be deployed in one of these configurations:
1. **SSH Tunnel Access** (Recommended) - Run on administrator's local machine, connect via SSH tunnel to mail server
2. **Same Server Installation** - Run directly on the mail server, accessible only via private IP
3. **Internal Network Only** - Deploy on internal network server, never expose to public internet

### Security Warnings
- ❌ **DO NOT** expose this application to the public internet
- ❌ **DO NOT** run on a publicly accessible web server
- ❌ **DO NOT** use without proper SSH key authentication
- ⚠️ Application has not been hardened against external attacks
- ⚠️ Designed for trusted administrator access only
- ⚠️ All connections to mail services should be via SSH tunnel or localhost

### Recommended Deployment
```bash
# Start SSH tunnel to mail server (from administrator's machine)
./scripts/ssh_tunnel.sh

# Run application locally (only accessible from localhost)
./scripts/start.sh

# Access via browser at http://localhost:8000
```

## 🔒 CRITICAL SAFETY FEATURES

### 1. SANDBOX MODE BY DEFAULT
**ALL OPERATIONS RUN IN SANDBOX MODE BY DEFAULT!**
- ✅ No production changes without explicit `--execute-real` flag
- ✅ Safe testing environment for all operations  
- ✅ Review what would happen before applying to production
- ✅ Staging database tables for risk-free testing
- 📖 See [SANDBOX_SAFETY.md](docs/SANDBOX_SAFETY.md) for complete details

### 2. MANDATORY BACKUP SYSTEM
**ALL PRODUCTION CHANGES REQUIRE VERIFIED BACKUPS!**
- ✅ Automatic backups before ANY mail5 changes
- ✅ Backup verification (checksum + integrity)
- ✅ Clear restoration instructions provided
- ✅ Operations abort if backups fail
- 📖 See [BACKUP_SAFETY.md](docs/BACKUP_SAFETY.md) for complete details

### Quick Example
```bash
# SAFE - Runs in sandbox, shows what would happen
posogoadmin user create test@example.com

# PRODUCTION - Only with explicit flag and confirmation
posogoadmin --execute-real user create test@example.com
```

## Overview

PoDoSoGoAdmin provides a clean, secure interface for managing email users in a SOGo + Postfix + Dovecot environment. Built with Python and FastAPI, it offers both REST API and command-line interfaces for comprehensive mail administration.

## Features

### Completed (Production Ready)
- ✅ User creation with secure password hashing (SHA512-crypt)
- ✅ User deletion with cleanup
- ✅ User listing and search
- ✅ Password reset functionality
- ✅ User info editing (name, email)
- ✅ Domain management (create, list, delete)
- ✅ Alias management (create, update, delete, distribution lists)
- ✅ REST API with JWT authentication
- ✅ CLI for scripting and automation
- ✅ Sandbox mode for safe testing
- ✅ Automatic backup system
- ✅ AWS EC2 snapshot integration
- ✅ Service reload integration (Postfix/Dovecot/SSH-Tunnels/PostFix/Nginx/SOGo)
- ✅ **Multi-Server Monitoring** (24/7 background monitoring and alerting, integration possible with Nagios, Grafana, Prometheus and other tools)
- ✅ **Enhanced Performance Metrics** (CPU, memory, disk, network, database)
- ✅ **Alert Management System** (thresholds, notifications, auto-resolution)
- ✅ **Email Traffic Analytics** (flow visualization, hourly/daily metrics)
- ✅ **Historical Data Collection** (90-day retention, SQLite storage)
- ✅ **Real-time Service Health** (PostgreSQL, Postfix, Dovecot, SOGo)
- ✅ **Dual Server Support** (mail.domain1.com + mail.domain2.net)

### Phase 2 (Planned)
- [ ] Web UI dashboard
- [ ] Bulk user import/export (CSV)
- [ ] Quota management
- [ ] Vacation/out-of-office settings
- [ ] Access control (admin roles)
- [ ] Audit logging

### Phase 3 (Future)
- [ ] Calendar/Contact folder management
- [ ] User migration tools
- [ ] Advanced backup/restore functionality
- [ ] LDAP integration option

## Technology Stack

- **Language**: Python 3.10+
- **Web Framework**: FastAPI
- **CLI Framework**: Typer
- **Database**: PostgreSQL (via asyncpg/SQLAlchemy)
- **Authentication**: OAuth2 with JWT tokens
- **Password Hashing**: passlib with SHA512-crypt (SOGo compatible)
- **Web UI**: Jinja2 templates with HTMX (no JavaScript frameworks)
- **Testing**: pytest with coverage

## Architecture

```
PoDoSoGoAdmin
├── API Layer (FastAPI)
│   ├── REST endpoints for user management
│   ├── OpenAPI/Swagger documentation
│   └── JWT authentication
├── Web UI (FastAPI + Jinja2)
│   ├── Admin dashboard
│   ├── User management forms
│   └── HTMX for dynamic updates
├── CLI (Typer)
│   ├── User CRUD operations
│   ├── Bulk operations
│   └── System maintenance
└── Core (Business Logic)
    ├── Database models (SQLAlchemy)
    ├── SOGo integration
    ├── Password hashing
    └── Email validation
```

## Database Integration

PoDoSoGoAdmin works with the existing SOGo database schema:

### Primary Tables
- `sogo_users` - Main user authentication table
- `sogo_user_profile` - User preferences
- Per-user calendar/contact tables (created by SOGo)

### Compatibility
- Maintains compatibility with SOGo's authentication system
- Uses same password hashing algorithm (SHA512-crypt with 70000 rounds)
- Preserves existing table structures

## Installation

### Prerequisites
- Python 3.10 or higher
- PostgreSQL 12+
- Running SOGo instance
- SSH access to mail server (for service reloads)
- So far only tested on Debian 12 and variants like Q4OS

### Quick Start
```bash
# Clone repository
git clone [https://github.com/dev2devportal/podosogadmin/podosogoadmin.git](https://github.com/dev2devportal/podosogadmin/posogoadmin.git)
cd posogoadmin

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure database connection
cp config/config.example.yaml config/config.yaml
# Edit config/config.yaml with your settings

# Run database migrations
python src/manage.py migrate

# Start web server
python src/main.py

# Or use CLI
python src/cli.py --help
```

## Configuration

Configuration is stored in `config/config.yaml`:

```yaml
database:
  host: localhost
  port: 5432
  name: sogo
  user: sogo
  password: your_password

mail_server:
  host: xxx.xxx.xxx.xxx
  ssh_user: admin
  ssh_key: ~/.ssh/yoursshkey

sogo:
  password_rounds: 70000
  default_domain: domain1.com

api:
  secret_key: your-secret-key-here
  token_expire_minutes: 60
```

## Usage

### Web Interface
Navigate to `http://localhost:8000` after starting the server.

### CLI Examples
```bash
# Create a new user
posogoadmin user create --email john@example.com --name "John Doe"

# List all users
posogoadmin user list

# Reset password
posogoadmin user reset-password --email john@example.com

# Delete user
posogoadmin user delete --email john@example.com

# Bulk import
posogoadmin user import --csv users.csv
```

## Development

### Setup Development Environment
```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
pytest

# Run with coverage
pytest --cov=src

# Format code
black src/ tests/

# Type checking
mypy src/
```

### Project Structure
```
posogoadmin/
├── src/
│   ├── api/          # FastAPI routes
│   ├── cli/          # Typer CLI commands
│   ├── core/         # Business logic
│   ├── db/           # Database models and queries
│   ├── templates/    # Jinja2 templates
│   ├── static/       # CSS, images
│   ├── config.py     # Configuration management
│   └── main.py       # Application entry point
├── tests/            # Test suite
├── docs/             # Documentation
├── scripts/          # Utility scripts
├── config/           # Configuration files
└── requirements.txt  # Python dependencies
```

## Security Considerations

- Passwords are hashed using SHA512-crypt with 70000 rounds (SOGo compatible)
- API uses JWT tokens for authentication
- All database connections use prepared statements
- SSH connections use key-based authentication
- Configuration files should be protected (chmod 600)

## License

GPL-3.0 (Compatible with SOGo's GPL-2+ license)

## Contributing

Contributions are welcome! Please submit pull requests with tests and documentation.

## Support

For issues and questions:
- Create an issue in the repository
- Contact: support@dev2dev.net

## Acknowledgments

- SOGo project for the excellent groupware platform
- PostfixAdmin for inspiration on mail user management
- FastAPI for the modern Python web framework
