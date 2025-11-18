# Comprehensive Dokku Cheat Sheet

## Installation & Setup

```bash
# Install Dokku (Ubuntu)
wget https://raw.githubusercontent.com/dokku/dokku/v0.34.0/bootstrap.sh
sudo DOKKU_TAG=v0.34.0 bash bootstrap.sh

# Setup SSH key
cat ~/.ssh/id_rsa.pub | ssh root@dokku.me "sudo dokku ssh-keys:add admin"

# Setup domain
dokku domains:set-global dokku.me
```

## Core App Management

```bash
# Create app
dokku apps:create myapp

# List apps
dokku apps:list

# Destroy app
dokku apps:destroy myapp

# Rename app
dokku apps:rename oldname newname

# Lock/unlock app (prevent deployments)
dokku apps:lock myapp
dokku apps:unlock myapp

# Clone app
dokku apps:clone myapp myapp-staging

# App info
dokku apps:report myapp
```

## Deployment

```bash
# Deploy via Git
git remote add dokku dokku@dokku.me:myapp
git push dokku main

# Deploy from tar archive
dokku git:from-archive myapp https://example.com/app.tar.gz

# Deploy from Docker image
dokku git:from-image myapp node:18

# Rebuild app
dokku ps:rebuild myapp

# Set buildpack
dokku buildpacks:set myapp https://github.com/heroku/heroku-buildpack-nodejs
```

## Process Management

```bash
# Start/stop/restart app
dokku ps:start myapp
dokku ps:stop myapp
dokku ps:restart myapp

# Scale processes
dokku ps:scale myapp web=2 worker=1

# View running processes
dokku ps:report myapp

# Enter container
dokku enter myapp web

# Run command in container
dokku run myapp npm test

# Set restart policy
dokku ps:set-restart-policy myapp unless-stopped
```

## Domain Management

```bash
# Add domain
dokku domains:add myapp example.com
dokku domains:add myapp www.example.com

# Remove domain
dokku domains:remove myapp example.com

# List domains
dokku domains:report myapp

# Clear all domains
dokku domains:clear myapp

# Enable/disable VHOST
dokku domains:enable myapp
dokku domains:disable myapp
```

## SSL/TLS (Let's Encrypt)

```bash
# Install Let's Encrypt plugin
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git

# Set email for Let's Encrypt
dokku letsencrypt:set myapp email your@email.com

# Enable SSL
dokku letsencrypt:enable myapp

# Auto-renew SSL
dokku letsencrypt:auto-renew myapp

# Revoke certificate
dokku letsencrypt:revoke myapp

# List certificates
dokku letsencrypt:list
```

## Environment Variables

```bash
# Set environment variable
dokku config:set myapp KEY=value KEY2=value2

# Set without restart
dokku config:set --no-restart myapp KEY=value

# Get all variables
dokku config:show myapp

# Get specific variable
dokku config:get myapp KEY

# Unset variable
dokku config:unset myapp KEY

# Export to file
dokku config:export myapp > .env

# Import from file
dokku config:set myapp $(cat .env | xargs)
```

## Port Management

```bash
# Add port mapping
dokku ports:add myapp http:80:5000

# Remove port mapping
dokku ports:remove myapp http:80:5000

# List port mappings
dokku ports:report myapp

# Clear all port mappings
dokku ports:clear myapp

# Set port scheme
dokku ports:set myapp http 80:5000 443:5000
```

## Storage/Volumes

```bash
# Create persistent storage
dokku storage:ensure-directory myapp

# Mount directory
dokku storage:mount myapp /var/lib/dokku/data/storage/myapp:/app/storage

# List mounts
dokku storage:report myapp

# Unmount
dokku storage:unmount myapp /var/lib/dokku/data/storage/myapp:/app/storage
```

## Database Plugins

### PostgreSQL

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git

# Create database
dokku postgres:create mydb

# Link to app
dokku postgres:link mydb myapp

# Unlink
dokku postgres:unlink mydb myapp

# Destroy database
dokku postgres:destroy mydb

# Export/Import
dokku postgres:export mydb > backup.sql
dokku postgres:import mydb < backup.sql

# Connect to database
dokku postgres:connect mydb

# Info
dokku postgres:info mydb

# List databases
dokku postgres:list

# Start/stop database
dokku postgres:start mydb
dokku postgres:stop mydb
```

### MySQL

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-mysql.git

# Create database
dokku mysql:create mydb

# Link to app
dokku mysql:link mydb myapp

# Export/Import
dokku mysql:export mydb > backup.sql
dokku mysql:import mydb < backup.sql

# Connect
dokku mysql:connect mydb
```

### MongoDB

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-mongo.git

# Create database
dokku mongo:create mydb

# Link to app
dokku mongo:link mydb myapp

# Export/Import
dokku mongo:export mydb > backup.tar
dokku mongo:import mydb < backup.tar
```

### Redis

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-redis.git

# Create Redis instance
dokku redis:create myredis

# Link to app
dokku redis:link myredis myapp

# Info
dokku redis:info myredis

# Connect
dokku redis:connect myredis
```

### Elasticsearch

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-elasticsearch.git

# Create instance
dokku elasticsearch:create myes

# Link to app
dokku elasticsearch:link myes myapp
```

## Proxy Management (Nginx)

```bash
# Enable/disable proxy
dokku proxy:enable myapp
dokku proxy:disable myapp

# Set proxy type
dokku proxy:set myapp nginx

# Build proxy config
dokku proxy:build-config myapp

# Show ports
dokku proxy:ports myapp

# Custom nginx config
dokku nginx:set myapp client-max-body-size 50m

# Show logs
dokku nginx:access-logs myapp
dokku nginx:error-logs myapp
```

## Cron Jobs

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-cron.git

# List cron jobs
dokku cron:list myapp

# Add cron job (use crontab syntax in app)
# Create file in repo: DOKKU_SCALE or .dokku-cron
# Format: */5 * * * * /app/script.sh
```

## Maintenance Mode

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-maintenance.git

# Enable maintenance
dokku maintenance:enable myapp

# Disable maintenance
dokku maintenance:disable myapp

# Custom page
dokku maintenance:custom-page myapp < maintenance.html
```

## Logs

```bash
# View logs
dokku logs myapp

# Follow logs
dokku logs myapp -t

# Limit number of lines
dokku logs myapp -n 100

# View specific process logs
dokku logs myapp web

# Failed deployment logs
dokku logs myapp --failed
```

## Network Management

```bash
# Create network
dokku network:create mynetwork

# Destroy network
dokku network:destroy mynetwork

# Set network for app
dokku network:set myapp attach-post-create mynetwork

# List networks
dokku network:report myapp

# Rebuild network
dokku network:rebuild myapp
```

## Checks & Health

```bash
# Skip checks during deployment
dokku checks:disable myapp

# Enable checks
dokku checks:enable myapp

# Run checks manually
dokku checks:run myapp

# Configure checks in app with CHECKS file:
# WAIT=5
# ATTEMPTS=10
# /health-check expected content
```

## Docker Options

```bash
# Add Docker option
dokku docker-options:add myapp deploy "--memory=1g"
dokku docker-options:add myapp run "--env=DEBUG=true"

# Remove option
dokku docker-options:remove myapp deploy "--memory=1g"

# List options
dokku docker-options:report myapp
```

## Plugin Management

```bash
# List installed plugins
dokku plugin:list

# Install plugin
sudo dokku plugin:install <repo-url>

# Update plugin
sudo dokku plugin:update <plugin-name>

# Uninstall plugin
sudo dokku plugin:uninstall <plugin-name>

# Update all plugins
sudo dokku plugin:update
```

## Backup & Restore

```bash
# App config backup
dokku config:export myapp > config-backup.txt

# Database backups (with postgres plugin)
dokku postgres:export mydb > db-backup.sql

# Full app clone
dokku apps:clone myapp myapp-backup

# Storage backup
tar -czf storage-backup.tar.gz /var/lib/dokku/data/storage/myapp
```

## Resource Limits

```bash
# Set memory limit
dokku resource:limit myapp --memory 512m

# Set CPU limit
dokku resource:limit myapp --cpu-quota 50000

# Reserve resources
dokku resource:reserve myapp --memory 256m

# Show limits
dokku resource:report myapp
```

## Events & Hooks

```bash
# View event logs
dokku events:list

# Follow events
dokku events:list --tail

# Trigger custom event
dokku events:trigger myapp custom-event
```

## User Management

```bash
# Add SSH key
dokku ssh-keys:add keyname path/to/key.pub

# List SSH keys
dokku ssh-keys:list

# Remove SSH key
dokku ssh-keys:remove keyname
```

## Registry (Docker Registry)

```bash
# Install plugin
sudo dokku plugin:install https://github.com/dokku/dokku-registry.git

# Login to registry
dokku registry:login myapp docker.io username password

# Set registry
dokku registry:set myapp server docker.io

# Push to registry
dokku registry:push myapp
```

## Common Workflows

### Deploy with PostgreSQL
```bash
# Create app and database
dokku apps:create myapp
dokku postgres:create myapp-db
dokku postgres:link myapp-db myapp

# Deploy
git remote add dokku dokku@server:myapp
git push dokku main

# Add domain and SSL
dokku domains:add myapp example.com
dokku letsencrypt:enable myapp
```

### Zero-Downtime Deployment
```bash
# Enable checks (CHECKS file in repo)
dokku checks:enable myapp

# Scale to multiple instances
dokku ps:scale myapp web=3

# Deploy - old containers stay until new ones pass checks
git push dokku main
```

### Environment-based Config
```bash
# Staging
dokku config:set myapp-staging NODE_ENV=staging

# Production
dokku config:set myapp NODE_ENV=production
dokku resource:limit myapp --memory 1g
```

## Troubleshooting

```bash
# View app report
dokku apps:report myapp

# Check app logs
dokku logs myapp -t

# Rebuild app
dokku ps:rebuild myapp

# Inspect container
dokku enter myapp

# Check nginx config
dokku nginx:validate-config myapp

# Clear cache
dokku repo:purge-cache myapp

# Reset app (dangerous!)
dokku apps:destroy myapp
dokku apps:create myapp
```

## Configuration Files in App Repo

### Procfile
```
web: npm start
worker: npm run worker
release: npm run migrate
```

### CHECKS
```
WAIT=5
ATTEMPTS=10
TIMEOUT=5
/health-check
```

### app.json
```json
{
  "name": "myapp",
  "scripts": {
    "dokku": {
      "predeploy": "npm run migrate",
      "postdeploy": "npm run seed"
    }
  }
}
```

### .buildpacks
```
https://github.com/heroku/heroku-buildpack-nodejs
https://github.com/heroku/heroku-buildpack-ruby
```

---

**Documentation**: https://dokku.com/docs/
**Plugins**: https://dokku.com/docs/community/plugins/
