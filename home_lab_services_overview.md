# 🏠 Home Lab Services - Comprehensive Breakdown

## 🛠️ Development Services

### **Web Development Stack**
**Nginx (Port 8080)**
- **Purpose**: High-performance web server and reverse proxy
- **What it does**: Serves static files, handles HTTP requests, acts as a load balancer
- **In your lab**: Serves your development websites, handles PHP requests by forwarding to PHP-FPM
- **Why you need it**: Industry standard web server, fast and reliable for hosting applications

**PHP-FPM**
- **Purpose**: FastCGI Process Manager for PHP
- **What it does**: Executes PHP code efficiently in separate processes
- **In your lab**: Processes PHP scripts for your web applications
- **Why you need it**: Better performance and resource management than traditional PHP handlers

### **Database Management**
**Adminer (Port 8081)**
- **Purpose**: Lightweight database management tool
- **What it does**: Web-based interface to manage multiple database types (MySQL, PostgreSQL, MongoDB)
- **In your lab**: Single interface to manage all your databases
- **Why you need it**: Simpler alternative to phpMyAdmin, supports multiple DB engines

**phpMyAdmin (Port 8082)**
- **Purpose**: Web-based MySQL/MariaDB administration tool
- **What it does**: Create databases, manage tables, run SQL queries, import/export data
- **In your lab**: Dedicated MySQL management interface
- **Why you need it**: More feature-rich than Adminer for MySQL-specific tasks

**MongoDB Express (Port 8084)**
- **Purpose**: Web-based MongoDB admin interface
- **What it does**: Browse collections, edit documents, run queries on MongoDB
- **In your lab**: GUI for managing your NoSQL MongoDB databases
- **Why you need it**: MongoDB doesn't have a built-in web interface like MySQL

### **Database Engines**
**MySQL/MariaDB (Port 3306)**
- **Purpose**: Relational database management system
- **What it does**: Stores structured data in tables with relationships
- **In your lab**: Primary database for web applications, WordPress sites, etc.
- **Why you need it**: Most web applications expect MySQL compatibility

**PostgreSQL (Port 5432)**
- **Purpose**: Advanced open-source relational database
- **What it does**: More advanced features than MySQL (JSON support, full-text search, etc.)
- **In your lab**: For applications requiring advanced database features
- **Why you need it**: Better for complex applications, data analytics, GIS applications

**MongoDB (Port 27017)**
- **Purpose**: NoSQL document database
- **What it does**: Stores data as flexible JSON-like documents
- **In your lab**: For applications that need flexible, schema-less data storage
- **Why you need it**: Modern applications often need flexible data structures

**Redis (Port 6379)**
- **Purpose**: In-memory data structure store
- **What it does**: Caching, session storage, message queuing
- **In your lab**: Speeds up applications by caching frequently accessed data
- **Why you need it**: Dramatically improves application performance

### **Development Tools**
**Mailhog (Port 8025)**
- **Purpose**: Email testing tool for developers
- **What it does**: Captures emails sent by your applications without actually sending them
- **In your lab**: Test email functionality without spamming real email addresses
- **Why you need it**: Essential for testing registration, password reset, notification features

---

## 🏗️ Infrastructure Services

### **Container Management**
**Portainer (Port 9000)**
- **Purpose**: Docker container management GUI
- **What it does**: Visual interface to manage Docker containers, images, networks, volumes
- **In your lab**: Easy way to monitor and control all your Docker services
- **Why you need it**: Much easier than command-line Docker management

**Nginx Proxy Manager (Port 81)**
- **Purpose**: Reverse proxy with SSL management
- **What it does**: Routes requests to different services, handles SSL certificates automatically
- **In your lab**: Access services via custom domains, automatic HTTPS certificates
- **Why you need it**: Professional setup with proper SSL, easier than manual Nginx configuration

---

## 🔄 CI/CD (Continuous Integration/Continuous Deployment)

### **Version Control**
**Gitea (Port 3000)**
- **Purpose**: Self-hosted Git service (like GitHub)
- **What it does**: Git repository hosting, issue tracking, pull requests, user management
- **In your lab**: Your own private GitHub for personal/business projects
- **Why you need it**: Keep code private, full control over your repositories

### **Build Automation**
**Jenkins (Port 8085)**
- **Purpose**: Automation server for CI/CD pipelines
- **What it does**: Automatically builds, tests, and deploys code when you push to Git
- **In your lab**: Automates your development workflow from code commit to deployment
- **Why you need it**: Professional development practices, reduces manual deployment errors

---

## 📊 Monitoring & Observability

### **Metrics & Monitoring**
**Prometheus (Port 9090)**
- **Purpose**: Time-series database and monitoring system
- **What it does**: Collects and stores metrics from all your services
- **In your lab**: Tracks CPU usage, memory, disk space, service uptime
- **Why you need it**: Know when services are having problems before they fail

**Grafana (Port 3001)**
- **Purpose**: Data visualization and dashboards
- **What it does**: Creates beautiful charts and graphs from Prometheus data
- **In your lab**: Visual dashboards showing system health, performance trends
- **Why you need it**: Easy-to-understand visual representation of your system's health

**Node Exporter (Port 9100)**
- **Purpose**: System metrics collector
- **What it does**: Exports hardware and OS metrics to Prometheus
- **In your lab**: Monitors CPU, memory, disk, network of your host system
- **Why you need it**: Essential system monitoring data

**cAdvisor (Port 8086)**
- **Purpose**: Container metrics collector
- **What it does**: Monitors Docker container resource usage and performance
- **In your lab**: Shows which containers are using the most resources
- **Why you need it**: Identify resource-hungry containers, optimize performance

### **Logging**
**Elasticsearch (Port 9200)**
- **Purpose**: Search and analytics engine
- **What it does**: Stores and indexes log data for fast searching
- **In your lab**: Central storage for all application and system logs
- **Why you need it**: Find specific errors across all services quickly

**Logstash (Port 5044)**
- **Purpose**: Log processing pipeline
- **What it does**: Collects, parses, and transforms logs before storing in Elasticsearch
- **In your lab**: Standardizes log formats from different services
- **Why you need it**: Makes logs from different services searchable in a consistent way

**Kibana (Port 5601)**
- **Purpose**: Data visualization for Elasticsearch
- **What it does**: Search logs, create visualizations, build dashboards
- **In your lab**: Web interface to search through all your system logs
- **Why you need it**: Essential for troubleshooting issues across your entire stack

---

## 🏠 Home Services

### **Personal Cloud**
**NextCloud (Port 8090)**
- **Purpose**: Self-hosted cloud storage (like Google Drive/Dropbox)
- **What it does**: File storage, sharing, synchronization, calendar, contacts
- **In your lab**: Your own private cloud, accessible from anywhere
- **Why you need it**: Data privacy, no subscription fees, unlimited storage

### **Media Services**
**Jellyfin (Port 8096)**
- **Purpose**: Media server (like Plex/Netflix for your content)
- **What it does**: Streams movies, TV shows, music to any device
- **In your lab**: Access your media collection from phones, tablets, smart TVs
- **Why you need it**: Stream your personal media collection anywhere

### **Home Automation**
**Home Assistant (Port 8123)**
- **Purpose**: Open-source home automation platform
- **What it does**: Controls smart home devices, creates automations, provides dashboards
- **In your lab**: Central hub for all smart home devices and automations
- **Why you need it**: Privacy-focused alternative to Google/Amazon smart home platforms

**Mosquitto (MQTT Broker) (Port 1883)**
- **Purpose**: Message broker for IoT devices
- **What it does**: Enables communication between smart home devices
- **In your lab**: Communication backbone for Home Assistant and IoT devices
- **Why you need it**: Many smart home devices use MQTT protocol

**Node-RED (Port 1880)**
- **Purpose**: Visual programming tool for IoT
- **What it does**: Create automation flows using drag-and-drop interface
- **In your lab**: Build complex home automations without coding
- **Why you need it**: Easier way to create sophisticated automations

### **Network Services**
**Pi-hole (Port 8089)**
- **Purpose**: Network-wide ad blocker and DNS server
- **What it does**: Blocks ads and tracking at the DNS level for entire network
- **In your lab**: Blocks ads on all devices connected to your network
- **Why you need it**: Network-wide ad blocking, improves browsing speed and privacy

**WireGuard (Port 51820)**
- **Purpose**: Modern VPN solution
- **What it does**: Secure remote access to your home network
- **In your lab**: Access all your home lab services securely from anywhere
- **Why you need it**: Secure remote access, simpler and faster than traditional VPNs

---

## 💾 Backup & Security

### **Backup**
**Duplicati (Port 8200)**
- **Purpose**: Backup solution with encryption and deduplication
- **What it does**: Automated backups to local/cloud storage with encryption
- **In your lab**: Protects your data with scheduled, encrypted backups
- **Why you need it**: Data loss protection, supports multiple backup destinations

### **Security**
**Trivy (Port 4954)**
- **Purpose**: Container vulnerability scanner
- **What it does**: Scans Docker images for security vulnerabilities
- **In your lab**: Identifies security issues in your container images
- **Why you need it**: Security best practice, identifies vulnerable software

**Watchtower**
- **Purpose**: Automatic container updater
- **What it does**: Automatically updates Docker containers to latest versions
- **In your lab**: Keeps your services updated with security patches
- **Why you need it**: Reduces security risk from outdated software

**Fail2ban**
- **Purpose**: Intrusion prevention system
- **What it does**: Automatically blocks IP addresses after failed login attempts
- **In your lab**: Protects SSH and web services from brute force attacks
- **Why you need it**: Essential security measure for internet-facing services

---

## 🎯 How They Work Together

### **Development Workflow**
1. **Code** in your favorite editor
2. **Commit** to Gitea repositories
3. **Jenkins** automatically builds and tests
4. **Deploy** to your Nginx development environment
5. **Monitor** performance with Prometheus/Grafana

### **Data Flow**
1. **Applications** store data in MySQL/PostgreSQL/MongoDB
2. **Redis** caches frequently accessed data
3. **Logs** flow to ELK stack for analysis
4. **Metrics** collected by Prometheus for monitoring

### **Security Layers**
1. **UFW Firewall** controls network access
2. **Fail2ban** blocks malicious IPs
3. **WireGuard VPN** for secure remote access
4. **Trivy** scans for vulnerabilities
5. **Watchtower** keeps everything updated

### **Home Integration**
1. **Pi-hole** blocks ads network-wide
2. **Home Assistant** controls smart devices
3. **MQTT** enables device communication
4. **Node-RED** creates automations
5. **NextCloud** provides private cloud storage
6. **Jellyfin** streams media to devices

This comprehensive setup gives you:
- **Professional development environment**
- **Enterprise-grade monitoring**
- **Complete home automation**
- **Privacy-focused services**
- **Robust security and backup**

Each service has a specific role, but they work together to create a powerful, self-hosted infrastructure that rivals commercial cloud services while giving you complete control and privacy.


## 🎯 Key Strengths of This Setup
1. Complete Development Environment

You can develop, test, and deploy applications entirely within your home lab
Multiple database engines support different types of applications
Proper CI/CD pipeline for professional development workflows

2. Enterprise-Grade Monitoring

Full observability stack (metrics, logs, traces)
Professional-level dashboards and alerting
Container and system-level monitoring

3. Privacy-First Home Services

Self-hosted alternatives to Google Drive, Plex, etc.
Complete control over your data
No subscription fees or data mining

4. Security-Focused Architecture

Multiple layers of protection
Automated updates and vulnerability scanning
Secure remote access via VPN

🚀 What This Setup Enables You To Do

Develop and host websites/applications professionally
Stream media to any device anywhere
Automate your home with privacy-focused solutions
Block ads network-wide for all devices
Access everything securely from anywhere via VPN
Monitor system health like a professional datacenter
Keep your data private with self-hosted cloud storage

This is essentially a personal cloud infrastructure that gives you capabilities that normally cost hundreds of dollars per month from cloud providers, all running on your own hardware with complete privacy and control.