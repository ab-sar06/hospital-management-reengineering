# Physical Architecture View

## Overview

The Physical Architecture View describes the deployment and runtime architecture of the Hospital Management System. It shows how the logical components are mapped to physical machines, servers, and infrastructure components.

---

## Deployment Architecture

### Current Monolithic Deployment

```
┌──────────────────────────────────────────────────────────────┐
│                    CLIENT TIER                               │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │ Doctor PC   │  │ Admin PC    │  │ Receptionist│          │
│  │             │  │             │  │   Terminal  │          │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘          │
│         │                │               │                   │
│         └────────────────┴───────────────┘                   │
└─────────────────────┬──────────────────────────────────────┘
                      │ (HTTP/HTTPS requests)
                      ↓
┌──────────────────────────────────────────────────────────────┐
│            WEB/APPLICATION SERVER TIER                        │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ Server: Tomcat/Jetty/JBoss                              ││
│  │                                                           ││
│  │ Running Applications:                                    ││
│  │ ├─ Patient Management Module                             ││
│  │ ├─ Doctor Management Module                              ││
│  │ ├─ Appointment System                                    ││
│  │ ├─ Pharmacy Module                                       ││
│  │ ├─ Laboratory Module                                     ││
│  │ ├─ Billing System                                        ││
│  │ ├─ User Management                                       ││
│  │ └─ Reporting Module                                      ││
│  │                                                           ││
│  │ Resources: CPU, Memory, Disk Space                       ││
│  └──────────────────────────────────────────────────────────┘│
└─────────────────────────────┬────────────────────────────────┘
                              │ (JDBC/SQL)
                              ↓
┌──────────────────────────────────────────────────────────────┐
│              DATABASE SERVER TIER                             │
│  ┌──────────────────────────────────────────────────────────┐│
│  │ Database Server: MySQL/PostgreSQL                        ││
│  │                                                           ││
│  │ Databases:                                              ││
│  │ ├─ Hospital_DB (Main Application Database)              ││
│  │ ├─ Audit_DB (Audit Logs)                                ││
│  │ ├─ Backup_DB (Backup Database)                          ││
│  │                                                           ││
│  │ Resources: High Storage, Memory for Caching              ││
│  └──────────────────────────────────────────────────────────┘│
└──────────────────────────────────────────────────────────────┘
```

---

## Server Configuration

### Web/Application Server

**Hardware Specifications:**
```
Server Name: AppServer-01
Operating System: Linux (Ubuntu/CentOS)
CPU: 8 Cores (2.4 GHz)
RAM: 16 GB
Storage: 500 GB SSD
Network: 1 Gbps
```

**Software Stack:**
```
Java Version: Java 8/11/17
Application Server: Apache Tomcat 9.x / JBoss EAP
Framework: Spring Framework / Jakarta EE
Build Tool: Maven 3.x
Logging: Log4j 2.x
```

**Deployed Artifacts:**
```
hospital-management.war (Web Archive)
    ├─ WEB-INF/
    │   ├─ web.xml (Deployment descriptor)
    │   ├─ classes/ (Application classes)
    │   └─ lib/ (JAR dependencies)
    ├─ jsp/ (JavaServer Pages)
    ├─ css/ (Stylesheets)
    ├─ js/ (JavaScript files)
    └─ images/ (Static resources)
```

---

### Database Server

**Hardware Specifications:**
```
Server Name: DBServer-01
Operating System: Linux (Ubuntu/CentOS)
CPU: 16 Cores (2.6 GHz)
RAM: 64 GB
Storage: 2 TB HDD + 500 GB SSD (Cache)
Network: 1 Gbps
RAID Configuration: RAID 10 (for redundancy)
```

**Database Configuration:**
```
Database Engine: MySQL 5.7 / 8.0 or PostgreSQL 12+
Default Charset: UTF-8
Max Connections: 500
Buffer Pool Size: 32 GB
Log Files: Binary logs, Error logs, Slow query logs
Backup: Daily automated backups
```

**Database Schema:**
```
Hospital_DB
├─ Tables for Core Entities (100+ tables)
│   ├─ patients
│   ├─ doctors
│   ├─ appointments
│   ├─ medicines
│   ├─ prescriptions
│   ├─ laboratory_tests
│   ├─ test_results
│   ├─ billing
│   ├─ payments
│   ├─ users
│   ├─ roles
│   └─ [other tables]
│
├─ Indexes (300+ indexes)
│   ├─ Primary Key Indexes
│   ├─ Foreign Key Indexes
│   ├─ Performance Indexes
│   └─ Search Indexes
│
├─ Views (20+ views)
│   ├─ Patient Summary View
│   ├─ Doctor Schedule View
│   ├─ Financial Report View
│   └─ [other views]
│
├─ Stored Procedures (50+ procedures)
│   ├─ Patient-related procedures
│   ├─ Appointment procedures
│   ├─ Billing procedures
│   └─ [other procedures]
│
├─ Triggers (30+ triggers)
│   ├─ Audit triggers
│   ├─ Update triggers
│   └─ Validation triggers
│
└─ Functions (20+ functions)
    ├─ Calculation functions
    ├─ Date functions
    └─ Utility functions
```

---

## Network Architecture

### Network Topology

```
┌─────────────────────────────────────────────────────────────┐
│                  INTERNET / WAN                             │
└──────────────────────┬──────────────────────────────────────┘
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ↓                             ↓
    ┌─────────────┐         ┌─────────────────┐
    │  Firewall   │         │ Load Balancer   │
    │ (Inbound)   │         │ (Optional)      │
    └──────┬──────┘         └────────┬────────┘
           │                         │
           └───────────┬─────────────┘
                       ↓
    ┌──────────────────────────────────────┐
    │    Hospital Network (Internal LAN)    │
    │                                       │
    │  ┌────────────────────────────────┐  │
    │  │ Web/App Server: 192.168.1.10   │  │
    │  └────────────────────────────────┘  │
    │                                       │
    │  ┌────────────────────────────────┐  │
    │  │ DB Server: 192.168.1.20        │  │
    │  └────────────────────────────────┘  │
    │                                       │
    │  ┌────────────────────────────────┐  │
    │  │ Backup Server: 192.168.1.30    │  │
    │  └────────────────────────────────┘  │
    │                                       │
    │  ┌────────────────────────────────┐  │
    │  │ File Server: 192.168.1.40      │  │
    │  └────────────────────────────────┘  │
    │                                       │
    └──────────────────────────────────────┘
```

---

## Storage Architecture

### File System Structure

```
/opt/hospital-management/
├─ application/
│   ├─ tomcat/
│   │   ├─ webapps/
│   │   │   └─ hospital-management.war
│   │   ├─ logs/
│   │   └─ conf/
│   ├─ java/
│   │   └─ jdk-17/
│   └─ config/
│       ├─ application.properties
│       ├─ database.properties
│       └─ log4j.xml
│
├─ data/
│   ├─ database/
│   │   └─ mysql/ (Database files)
│   ├─ backups/
│   │   ├─ daily/
│   │   ├─ weekly/
│   │   └─ monthly/
│   ├─ logs/
│   │   ├─ application.log
│   │   ├─ database.log
│   │   └─ audit.log
│   └─ uploads/
│       ├─ patient_documents/
│       ├─ medical_reports/
│       └─ prescriptions/
│
├─ backup/
│   ├─ database_backup.sql
│   ├─ file_backup.tar.gz
│   └─ backup_logs/
│
└─ scripts/
    ├─ startup.sh
    ├─ shutdown.sh
    ├─ backup.sh
    └─ maintenance.sh
```

---

## Performance Specifications

### Current System Capacity

```
┌─────────────────────────────────────────────────────────┐
│                 Performance Metrics                     │
├─────────────────────────────────────────────────────────┤
│ Concurrent Users Supported: 100-200                    │
│ Average Response Time: 2-5 seconds                     │
│ Database Query Time: 100-500 ms                        │
│ System Uptime: 99.5%                                   │
│ Daily Transactions: 10,000-50,000                      │
│ Storage Capacity: 500 GB available                     │
│ Backup Size: 50-100 GB                                 │
│ Network Bandwidth: 1 Gbps                              │
└─────────────────────────────────────────────────────────┘
```

---

## Security Architecture

### Security Layers

```
┌─────────────────────────────────────────────────────────┐
│              NETWORK SECURITY                           │
│  ├─ Firewall (Hardware/Software)                        │
│  ├─ Intrusion Detection System (IDS)                    │
│  ├─ VPN (for remote access)                             │
│  └─ DDoS Protection                                     │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│          APPLICATION SECURITY                           │
│  ├─ SSL/TLS Encryption                                  │
│  ├─ Authentication (Login)                              │
│  ├─ Authorization (Role-based access)                   │
│  ├─ Input Validation                                    │
│  └─ SQL Injection Prevention                            │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│          DATABASE SECURITY                              │
│  ├─ User Authentication                                 │
│  ├─ Privilege Management                                │
│  ├─ Data Encryption                                     │
│  ├─ Audit Logging                                       │
│  └─ Backup Encryption                                   │
└─────────────────────────────────────────────────────────┘
```

---

## Backup & Recovery Strategy

### Backup Schedule

```
Daily Backup:
├─ Time: 2:00 AM
├─ Type: Incremental
├─ Retention: 7 days
└─ Location: Backup Server

Weekly Backup:
├─ Time: Sunday 3:00 AM
├─ Type: Full
├─ Retention: 4 weeks
└─ Location: Backup Server

Monthly Backup:
├─ Time: 1st of month, 4:00 AM
├─ Type: Full
├─ Retention: 12 months
└─ Location: Off-site Storage
```

### Recovery Point Objective (RPO) & Recovery Time Objective (RTO)

```
RPO (Recovery Point Objective): 1 hour
└─ Data loss acceptable: up to 1 hour

RTO (Recovery Time Objective): 4 hours
└─ System should be operational within 4 hours
```

---

## Monitoring & Logging

### System Monitoring

```
Monitoring Tools:
├─ Nagios / Zabbix (Infrastructure monitoring)
├─ Application Performance Monitoring (APM)
├─ Database Monitoring Tools
├─ Network Monitoring
└─ Log Aggregation (ELK Stack / Splunk)

Metrics Tracked:
├─ CPU Usage
├─ Memory Usage
├─ Disk Space
├─ Network Bandwidth
├─ Response Time
├─ Error Rate
└─ Database Performance
```

### Logging Architecture

```
Log Files:
├─ Application Logs
│   ├─ ERROR logs
│   ├─ WARNING logs
│   ├─ INFO logs
│   └─ DEBUG logs
│
├─ Database Logs
│   ├─ Query logs
│   ├─ Error logs
│   └─ Slow query logs
│
├─ System Logs
│   ├─ Access logs
│   ├─ Security logs
│   └─ Audit logs
│
└─ Business Logs
    ├─ Transaction logs
    ├─ User activity logs
    └─ Financial transaction logs

Log Retention:
├─ Application: 90 days
├─ Database: 60 days
├─ System: 30 days
└─ Audit: 365 days
```

---

## Scalability Considerations

### Current Limitations

- Monolithic architecture
- Single database server
- Limited horizontal scaling
- Vertical scaling only

### Recommended Improvements

- Implement load balancing
- Database replication/clustering
- Separate read-write databases
- Implement caching layer (Redis)
- Database sharding for large datasets

---

## Disaster Recovery Plan

### Data Loss Prevention

```
Primary Strategy: Automated Backups
├─ Daily incremental backups
├─ Weekly full backups
├─ Off-site storage
└─ Encryption at rest

Secondary Strategy: Replication
├─ Database replication to standby server
├─ Real-time synchronization
└─ Automatic failover capability

Tertiary Strategy: Redundancy
├─ RAID configuration
├─ Multiple network connections
└─ UPS for power continuity
```

---

## Modernization Architecture Options

### Option 1: Cloud Deployment (AWS/Azure/GCP)

```
Load Balancer
    ↓
├─ Auto-scaling Application Servers
│   ├─ EC2 Instances (or equivalent)
│   └─ Container Orchestration (Kubernetes)
│
├─ Managed Database Service
│   ├─ RDS for MySQL/PostgreSQL
│   └─ Read replicas for scaling
│
├─ Storage Services
│   ├─ S3 for file storage
│   └─ CloudFront for CDN
│
└─ Managed Services
    ├─ Cache layer (ElastiCache/Redis)
    ├─ Message Queue (SQS/Kafka)
    └─ Monitoring (CloudWatch)
```

### Option 2: Microservices Architecture

```
API Gateway
    ├─ Patient Service (Docker Container)
    ├─ Doctor Service (Docker Container)
    ├─ Appointment Service (Docker Container)
    ├─ Pharmacy Service (Docker Container)
    ├─ Laboratory Service (Docker Container)
    └─ Billing Service (Docker Container)

Orchestration: Kubernetes
Message Bus: Apache Kafka
Cache: Redis
Service Discovery: Consul/Eureka
```

---

**Document Status:** Draft
**Version:** 1.0
**Last Updated:** 2026-06-11
**Next Section:** Process Architecture & Deployment
