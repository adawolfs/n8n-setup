# n8n-setup

A Docker Compose setup for running n8n with PostgreSQL database integration.

## Prerequisites

- Docker Engine (20.10.0 or higher)
- Docker Compose (v2.0.0 or higher)

## Quick Start

1. Clone this repository:
```bash
git clone https://github.com/adawolfs/n8n-setup.git
cd n8n-setup
```

2. Copy the environment file and customize if needed:
```bash
cp .env.example .env
```

3. Start the services:
```bash
docker compose up -d
```

4. Access n8n at `http://localhost:5678`

Default credentials:
- Username: `admin`
- Password: `admin`

## Configuration

### Environment Variables

Edit the `.env` file to customize your setup:

- **PostgreSQL Configuration:**
  - `POSTGRES_USER`: PostgreSQL username (default: `n8n`)
  - `POSTGRES_PASSWORD`: PostgreSQL password (default: `n8npassword`)
  - `POSTGRES_DB`: PostgreSQL database name (default: `n8n`)

- **n8n Configuration:**
  - `N8N_BASIC_AUTH_ACTIVE`: Enable basic authentication (default: `true`)
  - `N8N_BASIC_AUTH_USER`: n8n username (default: `admin`)
  - `N8N_BASIC_AUTH_PASSWORD`: n8n password (default: `admin`)
  - `N8N_HOST`: Hostname for n8n (default: `localhost`)
  - `N8N_PORT`: Port for n8n (default: `5678`)
  - `N8N_PROTOCOL`: Protocol (http/https) (default: `http`)
  - `WEBHOOK_URL`: Webhook URL for n8n (default: `http://localhost:5678/`)

## Usage

### Start Services
```bash
docker compose up -d
```

### Stop Services
```bash
docker compose down
```

### View Logs
```bash
docker compose logs -f
```

### View n8n Logs Only
```bash
docker compose logs -f n8n
```

### View PostgreSQL Logs Only
```bash
docker compose logs -f postgres
```

### Restart Services
```bash
docker compose restart
```

### Stop and Remove All Data
```bash
docker compose down -v
```

**Warning:** This will delete all your workflows and data!

## Data Persistence

All data is persisted in Docker volumes:
- `n8n-data`: Contains n8n workflows and configurations
- `postgres-data`: Contains PostgreSQL database files

These volumes persist even when containers are stopped or removed (unless you use `docker compose down -v`).

## Backup

To backup your n8n data:

1. Backup n8n data volume:
```bash
docker run --rm -v n8n-setup_n8n-data:/data -v $(pwd):/backup alpine tar czf /backup/n8n-backup.tar.gz -C /data .
```

2. Backup PostgreSQL data:
```bash
docker compose exec postgres pg_dump -U n8n n8n > n8n-postgres-backup.sql
```

## Restore

To restore from backup:

1. Restore n8n data volume:
```bash
docker run --rm -v n8n-setup_n8n-data:/data -v $(pwd):/backup alpine tar xzf /backup/n8n-backup.tar.gz -C /data
```

2. Restore PostgreSQL data:
```bash
cat n8n-postgres-backup.sql | docker compose exec -T postgres psql -U n8n -d n8n
```

## Troubleshooting

### Check if services are running
```bash
docker compose ps
```

### Check PostgreSQL connection
```bash
docker compose exec postgres psql -U n8n -d n8n -c "SELECT version();"
```

### Reset admin password
If you forgot your admin password, update it in the `.env` file and restart the services:
```bash
docker compose restart n8n
```

## Security Recommendations

For production use:
1. Change default passwords in `.env` file
2. Use strong, unique passwords
3. Consider using environment-specific `.env` files
4. Set up SSL/TLS for HTTPS access
5. Restrict network access using firewall rules
6. Regularly backup your data

## License

MIT License - see [LICENSE](LICENSE) file for details