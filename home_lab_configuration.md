# 🏠 Home Lab Docker Configuration

A comprehensive Docker-based home lab setup for development, monitoring, automation, and media services.

---

## 📋 Table of Contents

- [Prerequisites](#prerequisites)
- [Initial Server Setup](#initial-server-setup)
- [Development Environment](#development-environment)
- [CI/CD Pipeline](#cicd-pipeline)
- [Monitoring & Management](#monitoring--management)
- [Advanced Home Server Capabilities](#advanced-home-server-capabilities)
- [Security & Backup](#security--backup)
- [Service Access URLs](#service-access-urls)

---

## Prerequisites

- **OS**: Ubuntu 24.04.2 LTS

---

## Initial Server Setup

### 🔒 Server Hardening

```bash
# Update the system 
sudo apt update && sudo apt upgrade 

# Configure firewall 
sudo ufw allow ssh 
sudo ufw allow http 
sudo ufw allow https 
sudo ufw enable 

# Install fail2ban for SSH protection 
sudo apt install fail2ban 
sudo systemctl enable fail2ban
```

### 🐳 Docker Installation

```bash
# Install Docker 
sudo apt install docker.io docker-compose 

# Add your user to docker group (avoid using sudo for docker) 
sudo usermod -aG docker $USER 

# Create a structure for persistent data 
mkdir -p ~/docker/data 
mkdir -p ~/docker/compose
```

### 📊 Basic Monitoring Setup

```bash
# Create a docker-compose file for Portainer 
mkdir -p ~/docker/compose/portainer 
cd ~/docker/compose/portainer 
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3' 
services: 
  portainer: 
    image: portainer/portainer-ce:latest 
    container_name: portainer 
    restart: unless-stopped 
    ports: 
      - "9000:9000" 
    volumes: 
      - /var/run/docker.sock:/var/run/docker.sock 
      - portainer_data:/data 
volumes: 
  portainer_data:
```

```bash
docker-compose up -d
```

> **📌 Access Portainer**: Navigate to http://localhost:9000 or http://127.0.0.1:9000 and create your admin account when prompted.

---

## Development Environment

### 🛠️ Development Setup

```bash
# Install development essentials
sudo apt install build-essential git curl wget

# Set up multiple versions of programming languages with version managers:

# For Node.js:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

# For Python:
sudo apt install python3-pip
sudo apt install python3-venv
python3 -m venv ~/python-envs/venv

# Activate the venv
source ~/python-envs/venv/bin/activate

# Create a dedicated development directory structure
mkdir -p ~/projects/{web,api,experiments,tools}
```

### 🌐 Full Development Stack

```bash
mkdir -p ~/docker/dev-templates/web-stack
cd ~/docker/dev-templates/web-stack
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  # Web server (nginx)
  nginx:
    image: nginx:alpine
    container_name: dev_nginx
    ports:
      - "8080:80"
    volumes:
      - ./nginx/conf:/etc/nginx/conf.d
      - ./nginx/html:/usr/share/nginx/html
      - ./logs/nginx:/var/log/nginx
    networks:
      - dev-network
    depends_on:
      - php

  # PHP service
  php:
    image: php:8.1-fpm
    container_name: dev_php
    volumes:
      - ./src:/var/www/html
    networks:
      - dev-network
    depends_on:
      - mysql
      - redis

  # MySQL database
  mysql:
    image: mysql:8.0
    container_name: dev_mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: devdb
      MYSQL_USER: devuser
      MYSQL_PASSWORD: devpassword
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - dev-network

  # PostgreSQL database
  postgres:
    image: postgres:14
    container_name: dev_postgres
    restart: unless-stopped
    environment:
      POSTGRES_PASSWORD: postgrespassword
      POSTGRES_USER: postgresuser
      POSTGRES_DB: postgresdb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - dev-network

  # Redis cache
  redis:
    image: redis:alpine
    container_name: dev_redis
    ports:
      - "6379:6379"
    networks:
      - dev-network
    volumes:
      - redis_data:/data

  # MongoDB
  mongo:
    image: mongo:latest
    container_name: dev_mongo
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: adminpassword
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
    networks:
      - dev-network

  # MongoDB Express for MongoDB management
  mongo-express:
    image: mongo-express
    container_name: dev_mongo_express
    restart: unless-stopped
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: admin
      ME_CONFIG_MONGODB_ADMINPASSWORD: adminpassword  # Match MongoDB password
      ME_CONFIG_MONGODB_URL: mongodb://admin:adminpassword@mongodb:27017/
    ports:
      - "8084:8081"
    networks:
      - dev-network
    depends_on:
      - mongodb

  # Adminer (database management)
  adminer:
    image: adminer:latest
    container_name: dev_adminer
    restart: unless-stopped
    ports:
      - "8081:8080"
    networks:
      - dev-network
    depends_on:
      - mysql
      - postgres
      - mongo

  # Mailhog (email testing)
  mailhog:
    image: mailhog/mailhog
    container_name: dev_mailhog
    ports:
      - "1025:1025" # SMTP port
      - "8025:8025" # Web interface
    networks:
      - dev-network

networks:
  dev-network:
    driver: bridge

volumes:
  mysql_data:
  postgres_data:
  redis_data:
  mongo_data:
```

**Create Nginx configuration:**
```bash
mkdir -p ./nginx/conf
nano ./nginx/conf/default.conf
```

```nginx
server {
    listen 80;
    server_name localhost;
    root /usr/share/nginx/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME /var/www/html$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

**Create test HTML file:**
```bash
# Create directories and test file
mkdir -p ./nginx/html
echo '<h1>Development Stack is Working!</h1>' > ./nginx/html/index.html

# Start the development stack
docker-compose up -d
```

#### 🎯 Development Stack Features

This setup provides:
- **Nginx** web server
- **PHP-FPM** for PHP processing
- **MongoDB, MySQL and PostgreSQL** databases
- **Redis** cache
- **Adminer** for database management
- **Mailhog** for email testing

### 🔄 Reverse Proxy Setup

```bash
mkdir -p ~/docker/compose/nginx-proxy-manager
cd ~/docker/compose/nginx-proxy-manager
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  # Nginx Proxy Manager - handles multiple websites and SSL
  nginx-proxy-manager:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'      # HTTP
      - '443:443'    # HTTPS
      - '81:81'      # Admin panel
    volumes:
      - ./data/nginx/data:/data
      - ./data/nginx/letsencrypt:/etc/letsencrypt
    networks:
      - web-network

  # MariaDB - MySQL compatible database
  mariadb:
    image: mariadb:10.7
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: your_secure_password_here
      MYSQL_DATABASE: webdb
      MYSQL_USER: webuser
      MYSQL_PASSWORD: your_secure_db_password_here
    volumes:
      - ./data/mariadb:/var/lib/mysql
    networks:
      - web-network

  # phpMyAdmin - MariaDB Database management
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mariadb
      PMA_PORT: 3306
      MYSQL_ROOT_PASSWORD: your_secure_password_here
    networks:
      - web-network

  # Sample website container (for projects)
  website1:
    image: nginx:alpine
    restart: unless-stopped
    volumes:
      - ./websites/site1:/usr/share/nginx/html
    networks:
      - web-network

  # Portainer - Container management UI
  portainer:
    image: portainer/portainer-ce:latest
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./data/portainer:/data
    networks:
      - web-network

networks:
  web-network:
    driver: bridge
```

### 🗄️ Database Backend

```bash
mkdir -p ~/docker/compose/databases
cd ~/docker/compose/databases
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  # MariaDB (MySQL-compatible database)
  mariadb:
    image: mariadb:10.7
    container_name: db_mariadb
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword  # Change this!
      MYSQL_DATABASE: maindb
      MYSQL_USER: dbuser
      MYSQL_PASSWORD: dbpassword  # Change this!
    ports:
      - "3306:3306"
    volumes:
      - mariadb_data:/var/lib/mysql
      - ./mariadb/conf:/etc/mysql/conf.d
      - ./mariadb/initdb:/docker-entrypoint-initdb.d
    networks:
      - db-network

  # phpMyAdmin for MariaDB management
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: db_phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mariadb
      PMA_PORT: 3306
      MYSQL_ROOT_PASSWORD: rootpassword  # Match MariaDB password
    ports:
      - "8082:80"
    networks:
      - db-network
    depends_on:
      - mariadb

  # PostgreSQL database
  postgres:
    image: postgres:14
    container_name: db_postgres
    restart: unless-stopped
    environment:
      POSTGRES_PASSWORD: pgpassword  # Change this!
      POSTGRES_USER: pguser
      POSTGRES_DB: pgdatabase
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./postgres/initdb:/docker-entrypoint-initdb.d
    networks:
      - db-network

  # pgAdmin for PostgreSQL management
  pgadmin:
    image: dpage/pgadmin4
    container_name: db_pgadmin
    restart: unless-stopped
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com  # Change this!
      PGADMIN_DEFAULT_PASSWORD: pgadminpassword  # Change this!
    ports:
      - "8083:80"
    volumes:
      - pgadmin_data:/var/lib/pgadmin
    networks:
      - db-network
    depends_on:
      - postgres

  # MongoDB (NoSQL database)
  mongodb:
    image: mongo:latest
    container_name: db_mongodb
    restart: unless-stopped
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongouser
      MONGO_INITDB_ROOT_PASSWORD: mongopassword  # Change this!
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      - ./mongodb/initdb:/docker-entrypoint-initdb.d
    networks:
      - db-network

  # MongoDB Express for MongoDB management
  mongo-express:
    image: mongo-express
    container_name: db_mongo_express
    restart: unless-stopped
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: mongouser
      ME_CONFIG_MONGODB_ADMINPASSWORD: mongopassword  # Match MongoDB password
      ME_CONFIG_MONGODB_URL: mongodb://mongouser:mongopassword@mongodb:27017/
    ports:
      - "8084:8081"
    networks:
      - db-network
    depends_on:
      - mongodb

networks:
  db-network:
    driver: bridge

volumes:
  mariadb_data:
  postgres_data:
  pgadmin_data:
  mongodb_data:
```

**Create test database initialization:**
```bash
mkdir -p ./mariadb/initdb
nano ./mariadb/initdb/01-create-test-database.sql
```

```sql
-- Create a test database and user
CREATE DATABASE IF NOT EXISTS testdb;
CREATE USER IF NOT EXISTS 'testuser'@'%' IDENTIFIED BY 'testpassword';
GRANT ALL PRIVILEGES ON testdb.* TO 'testuser'@'%';
FLUSH PRIVILEGES;

-- Create a sample table
USE testdb;
CREATE TABLE IF NOT EXISTS sample_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert some test data
INSERT INTO sample_table (name) VALUES ('Sample 1'), ('Sample 2'), ('Sample 3');
```

---

## CI/CD Pipeline

### 🔧 Git Repository - Gitea

```bash
mkdir -p ~/docker/compose/gitea
cd ~/docker/compose/gitea
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: docker.gitea.com/gitea:1.23.8
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
```

```bash
docker-compose up -d
```

#### 🚀 Gitea Setup Instructions

1. **Wait** for database initialization (30-60 seconds)
2. **Open browser** to http://localhost:3000
3. **Configure initial settings**:
   - Database Type: SQLite
   - Host: gitea-db:3306
   - Username: gitea
   - Password: ****
   - Database Name: gitea

4. **General Settings**:
   - Site Title: "Your Development Server"
   - Repository Root Path: Leave default
   - Git LFS Root Path: Leave default
   - Server Domain: localhost
   - SSH Server Port: 222
   - HTTP Port: 3000
   - Application URL: http://localhost:3000/

5. **Create admin account**:
   - Admin Username: (your preferred username)
   - Password: (strong password)
   - Email: (your email)

6. **Click "Install Gitea"**

#### 📚 First Repository Test

```bash
# Clone test repository
cd ~/projects/web
git clone http://localhost:3000/yourusername/test-project.git
cd test-project

# Make test commit
echo "# My Test Project" >> README.md
echo "This is working!" >> README.md
git add .
git commit -m "Update README"
git push
```

> **⚠️ Note**: Configure Git globally:
> ```bash
> git config --global user.name "Your Gitea Name"
> git config --global user.email "your.email@example.com"
> ```

### 🔐 Gitea SSH Setup

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"
# Hit enter at prompts to use defaults

# Create SSH config
nano ~/.ssh/config
```

**SSH Config:**
```
Host localhost
    HostName localhost
    Port 222
    User <gitea username>
    IdentityFile ~/.ssh/id_ed25519
```

```bash
# Display public key
cat ~/.ssh/id_ed25519.pub
# Copy the entire output
```

#### 🔑 Add SSH Key to Gitea

1. Go to Gitea at http://localhost:3000
2. Click your avatar (top-right corner)
3. Select "Settings"
4. Click "SSH / GPG Keys" in left sidebar
5. Click "Add Key"
6. Paste your public key into "Content" field
7. Give it a title like "Homelab"
8. Click "Add Key"

**Test the connection:**
```bash
# Test SSH connection
ssh -T -p 222 git@localhost

# Change existing repo to SSH
cd ~/projects/test-project-config
git remote set-url origin git@localhost:yourusername/test-project.git
```

### 🏗️ CI/CD Pipeline - Jenkins

```bash
mkdir -p ~/docker/compose/ci
cd ~/docker/compose/ci
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    privileged: true
    user: root
    ports:
      - "8085:8080"
      - "50000:50000"
    volumes:
      - ./jenkins_data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - jenkins_network

networks:
  jenkins_network:
    driver: bridge
```

```bash
docker-compose up -d
```

#### 🔧 Jenkins Setup

1. **Access Jenkins** at http://localhost:8085
2. **Get admin password**: `docker-compose logs jenkins`
3. **Install suggested plugins**
4. **Configure Gitea integration**:
   - Install "Gitea Integration" plugin
   - Set up webhooks in Gitea repositories
   - Create pipeline jobs triggered on push events

> **📋 TODO**: Complete Gitea-Jenkins integration setup

---

## Monitoring & Management

### 📈 Prometheus & Grafana

```bash
mkdir -p ~/docker/compose/monitoring
cd ~/docker/compose/monitoring
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus/config:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    ports:
      - "9090:9090"
    networks:
      - monitoring_network

  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    ports:
      - "9100:9100"
    networks:
      - monitoring_network

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    restart: unless-stopped
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    ports:
      - "8086:8080"
    networks:
      - monitoring_network

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=secure_grafana_password
    ports:
      - "3001:3000"
    networks:
      - monitoring_network

networks:
  monitoring_network:
    driver: bridge

volumes:
  prometheus_data:
  grafana_data:
```

**Create Prometheus configuration:**
```bash
mkdir -p ./prometheus/config
nano ./prometheus/config/prometheus.yml
```

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

```bash
docker-compose up -d
```

### 📊 ELK Stack

```bash
mkdir -p ~/docker/compose/elk
cd ~/docker/compose/elk
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  elasticsearch:
    image: elasticsearch:8.18.1
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - elk_network

  logstash:
    image: logstash:8.18.1
    container_name: logstash
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - "5044:5044"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk_network
    depends_on:
      - elasticsearch

  kibana:
    image: kibana:8.18.1
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    networks:
      - elk_network
    depends_on:
      - elasticsearch

networks:
  elk_network:
    driver: bridge

volumes:
  elasticsearch_data:
```

**Create Logstash configuration:**
```bash
mkdir -p ./logstash/pipeline
nano ./logstash/pipeline/logstash.conf
```

```ruby
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}"
  }
}
```

---

## Advanced Home Server Capabilities

### ☁️ NextCloud

```bash
mkdir -p ~/docker/compose/nextcloud
cd ~/docker/compose/nextcloud
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  nextcloud-db:
    image: mariadb:10.7
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - nextcloud_db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=nextcloud_root_password
      - MYSQL_PASSWORD=nextcloud_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
    networks:
      - nextcloud_network

  nextcloud:
    image: nextcloud:latest
    container_name: nextcloud
    restart: unless-stopped
    depends_on:
      - nextcloud-db
    ports:
      - "8090:80"
    volumes:
      - nextcloud_data:/var/www/html
    environment:
      - MYSQL_PASSWORD=nextcloud_password
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=nextcloud-db
    networks:
      - nextcloud_network

networks:
  nextcloud_network:
    driver: bridge

volumes:
  nextcloud_db:
  nextcloud_data:
```

```bash
docker-compose up -d
```

### 🎬 Jellyfin Media Server

```bash
mkdir -p ~/docker/compose/jellyfin
cd ~/docker/compose/jellyfin
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    volumes:
      - jellyfin_config:/config
      - jellyfin_cache:/cache
      - /path/to/your/media:/media  # Replace with your media path
    ports:
      - "8096:8096"
      - "8920:8920"  # HTTPS port
      - "7359:7359/udp"  # Discovery service
      - "1900:1900/udp"  # DLNA service
    networks:
      - media_network

networks:
  media_network:
    driver: bridge

volumes:
  jellyfin_config:
  jellyfin_cache:
```

```bash
docker-compose up -d
```

> **📺 Access URL**: http://your-server-ip:8096

### 🏠 Home Automation with Home Assistant

```bash
mkdir -p ~/docker/compose/homeassistant
cd ~/docker/compose/homeassistant
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  homeassistant:
    image: ghcr.io/home-assistant/home-assistant:stable
    container_name: homeassistant
    restart: unless-stopped
    volumes:
      - homeassistant_config:/config
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "8123:8123"
    environment:
      - TZ=America/Los_Angeles  # Change to your timezone
    networks:
      - home_automation_network

  mosquitto:
    image: eclipse-mosquitto:latest
    container_name: mosquitto
    restart: unless-stopped
    volumes:
      - mosquitto_data:/mosquitto/data
      - mosquitto_log:/mosquitto/log
      - ./mosquitto/config:/mosquitto/config
    ports:
      - "1883:1883"
      - "9001:9001"
    networks:
      - home_automation_network

  nodered:
    image: nodered/node-red:latest
    container_name: nodered
    restart: unless-stopped
    volumes:
      - nodered_data:/data
    ports:
      - "1880:1880"
    networks:
      - home_automation_network

networks:
  home_automation_network:
    driver: bridge

volumes:
  homeassistant_config:
  mosquitto_data:
  mosquitto_log:
  nodered_data:
```

**Create Mosquitto configuration:**
```bash
mkdir -p ./mosquitto/config
nano ./mosquitto/config/mosquitto.conf
```

```
persistence true
persistence_location /mosquitto/data
log_dest file /mosquitto/log/mosquitto.log

# Allow anonymous connections (for testing only)
# For production, set up authentication
allow_anonymous true

listener 1883
protocol mqtt

listener 9001
protocol websockets
```

```bash
docker-compose up -d
```

> **🏠 Access URL**: http://your-server-ip:8123

### 🌐 Network Services - Pi-hole

```bash
mkdir -p ~/docker/compose/pihole
cd ~/docker/compose/pihole
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    restart: unless-stopped
    ports:
      - "8053:53/tcp"
      - "8053:53/udp"
      - "8089:80/tcp"
    environment:
      TZ: 'America/Los_Angeles'  # Change to your timezone
      WEBPASSWORD: 'pihole_admin_password'  # Change this!
      SERVERIP: '192.168.1.x'  # Change to your server's IP
    volumes:
      - pihole_etc:/etc/pihole
      - pihole_dnsmasq:/etc/dnsmasq.d
    cap_add:
      - NET_ADMIN
    networks:
      - pihole_network

networks:
  pihole_network:
    driver: bridge

volumes:
  pihole_etc:
  pihole_dnsmasq:
```

```bash
docker-compose up -d
```

> **🛡️ Access URL**: http://your-server-ip:8089
> 
> **⚠️ Note**: The password is automatically generated and can be found in the Pi-hole container logs.

### 🔒 WireGuard VPN Setup

```bash
mkdir -p ~/docker/compose/wireguard
cd ~/docker/compose/wireguard
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  wireguard:
    image: linuxserver/wireguard:latest
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles  # Change to your timezone
      - SERVERURL=auto  # Or specify your public IP or domain
      - SERVERPORT=51820
      - PEERS=3  # Number of client configurations to generate
      - PEERDNS=auto  # Use Pi-hole for DNS: 192.168.1.x (server IP)
      - INTERNAL_SUBNET=10.13.13.0
    volumes:
      - wireguard_config:/config
      - /lib/modules:/lib/modules
    ports:
      - "51820:51820/udp"
    restart: unless-stopped
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    networks:
      - vpn_network

networks:
  vpn_network:
    driver: bridge

volumes:
  wireguard_config:
    driver: local
```

```bash
docker-compose up -d
```

#### 📱 Mobile VPN Setup

1. **Install WireGuard** from App Store/Google Play
2. **View QR codes**: `docker-compose logs wireguard`
3. **Open WireGuard app**
4. **Tap "Add tunnel" or "+"**
5. **Scan QR code** with phone camera
6. **Toggle connection**: Home = OFF, Away = ON

Now you can access all home lab services remotely!

---

## Security & Backup

### 🔐 Security Stack

```bash
mkdir -p ~/docker/compose/security
cd ~/docker/compose/security
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  trivy:
    image: aquasec/trivy:latest
    container_name: trivy
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - trivy_cache:/root/.cache
    command: server --listen 0.0.0.0:4954
    ports:
      - "4954:4954"
    networks:
      - security_network

  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --schedule "0 0 4 * * *" --cleanup
    restart: unless-stopped
    networks:
      - security_network

networks:
  security_network:
    driver: bridge

volumes:
  trivy_cache:
```

```bash
docker-compose up -d
```

### 💾 Backup Solutions

```bash
mkdir -p ~/docker/compose/backup
cd ~/docker/compose/backup
nano docker-compose.yml
```

**docker-compose.yml:**
```yaml
version: '3'

services:
  duplicati:
    image: duplicati/duplicati:latest
    container_name: duplicati
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Los_Angeles # Change to your timezone
    volumes:
      - duplicati_config:/config
      - /home/s/backups:/backups          # Local backup storage location
      - /home/s:/source                   # Your entire home directory to backup
      - /home/s/docker:/docker:ro         # Your docker compose files (read-only)
      - /mnt/seagate:/external:ro         # Your external drive (read-only)
    ports:
      - "8200:8200"
    networks:
      - backup_network

networks:
  backup_network:
    driver: bridge

volumes:
  duplicati_config:
```

```bash
docker-compose up -d
```

#### 📦 Selective Backup Option

For more selective backup, use these volume mappings instead:

```yaml
volumes:
  - duplicati_config:/config
  - /home/s/backups:/backups
  - /home/s/docker:/docker:ro
  - /home/s/projects:/projects:ro
  - /home/s/.ssh:/ssh:ro
  - /home/s/.gitconfig:/gitconfig:ro
```

---

## Service Access URLs
```bash
| Service                   | URL                           | Port  | Purpose                    |
|---------------------------|-------------------------------|-------|----------------------------|
| **🛠️ Development**        |                               |       |                            |
| Web Development           | http://localhost:8080         | 8080  | Nginx dev server           |
| Adminer                   | http://localhost:8081         | 8081  | Database management        |
| phpMyAdmin                | http://localhost:8082         | 8082  | MySQL management           |
| MongoDB Express           | http://localhost:8084         | 8084  | MongoDB management         |
| Mailhog                   | http://localhost:8025         | 8025  | Email testing              |
| **🏗️ Infrastructure**     |                               |       |                            |
| Portainer                 | http://localhost:9000         | 9000  | Container management       |
| Nginx Proxy Manager       | http://localhost:81           | 81    | Reverse proxy admin        |
| **🔄 CI/CD**              |                               |       |                            |
| Gitea                     | http://localhost:3000         | 3000  | Git repository             |
| Jenkins                   | http://localhost:8087         | 8085  | CI/CD pipeline             |
| **📊 Monitoring**         |                               |       |                            |
| Prometheus                | http://localhost:9090         | 9090  | Metrics collection         |
| Grafana                   | http://localhost:3001         | 3001  | Dashboards                 |
| Kibana                    | http://localhost:5601         | 5601  | Log analysis               |
| cAdvisor                  | http://localhost:8086         | 8086  | Container metrics          |
| **🏠 Home Services**      |                               |       |                            |
| NextCloud                 | http://localhost:8090         | 8090  | Cloud storage              |
| Jellyfin                  | http://localhost:8096         | 8096  | Media server               |
| Home Assistant            | http://localhost:8123         | 8123  | Home automation            |
| Node-RED                  | http://localhost:1880         | 1880  | Automation flows           |
| Pi-hole                   | http://localhost:8089         | 8089  | DNS filtering              |
| **💾 Backup & Security**  |                               |       |                            |
| Duplicati                 | http://localhost:8200         | 8200  | Backup management          |
| Trivy                     | http://localhost:4954         | 4954  | Security scanning          |
```

## 🎯 Quick Start Commands

```bash
# Start all development services
cd ~/docker/dev-templates/web-stack && docker-compose up -d

# Start monitoring stack
cd ~/docker/compose/monitoring && docker-compose up -d

# Start home services
cd ~/docker/compose/nextcloud && docker-compose up -d
cd ~/docker/compose/jellyfin && docker-compose up -d

# Check all running containers
docker ps

# View logs for troubleshooting
docker-compose logs [service-name]

# Update all containers (via Watchtower)
# Automatic updates at 4 AM daily
```

---

*This home lab configuration provides a complete development, monitoring, and home automation environment using Docker containers. All services are isolated, scalable, and easily manageable through their respective web interfaces.*
