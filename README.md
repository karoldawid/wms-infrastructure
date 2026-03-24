# Enterprise Warehouse Management System (WMS) - Infrastructure & CI/CD

## 📌 Project Overview
This repository showcases the complete infrastructure design, network topology, high-availability (HA) configuration, and CI/CD automation for an Enterprise-class Warehouse Management System (WMS) based on a Multi-Page Application (MPA) architecture.

The environment was built from the ground up focusing on **fault tolerance, automated failover, secure network segmentation, and continuous delivery**.

## 🏗️ Architecture & Network Design

![Infrastructure Diagram](./diagrams/network-architecture.png)

### Security & Network Topology
* **Strict Segmentation:** Designed isolated logical networks separating external clients, the DMZ (Load Balancers & App Servers), and internal LANs (Databases & Storage).
* **Firewall & Routing:** Implemented strict zone-based filtering using `firewalld`, ensuring the principle of least privilege across all routing nodes.

### High Availability & Load Balancing (L4)
* **Active-Passive Load Balancers:** Configured a Layer-4 request router using **IPVS** (`ipvsadm`).
* **VRRP & Auto-Failover:** Implemented **Keepalived** for Virtual Router Redundancy Protocol (VRRP), enabling sub-second failover between master and backup load balancers with seamless IP takeover.
* **Health Checking:** Automated real-time health checks of backend Apache/Payara servers, automatically evicting dead nodes and re-adding them upon recovery.
* **Session Persistence:** Configured client-to-server affinity/persistence for specific logical networks.

## 💾 Database & Storage Tier

### Distributed Storage (GlusterFS)
* Deployed a 4-node **GlusterFS** distributed and replicated storage cluster.
* Ensured highly available, read/write persistent storage for application assets, resilient to single-node failures.
* Implemented NTP time synchronization across all cluster nodes.

### Database Cluster (PostgreSQL + PGPool-II)
* **Streaming Replication:** Configured a PostgreSQL cluster with synchronous streaming replication (Master-Standby).
* **Connection Pooling & Routing:** Implemented **PGPool-II** to manage the database cluster.
* **Read/Write Splitting:** Automated query routing (DDL/DML to Master, DQL to Standby) to optimize performance.
* **Automated DB Failover:** Configured PGPool-II to automatically promote the standby server in case of master node failure, ensuring zero-downtime for the application.

## 🚀 Containerization & CI/CD Automation


The application delivery process is fully automated using **Jenkins** (Infrastructure as Code via `Jenkinsfile`) and **Docker**.

* **Automated Pipeline:** Triggered via Git webhooks (using ngrok for exposure), the pipeline automates the build (Maven), test, and deployment phases.
* **Distributed Builds:** Jenkins manages remote Docker/Podman nodes via SSH agents.
* **Dynamic Provisioning:** The pipeline automatically provisions the database structures, injects initialization data, builds the `.war` artifact, and deploys it to the containerized **Payara Server**.
* **Automated QA (E2E):** Integrated parallel UI/Functional testing using **Selenide/Selenium**. Tests run across multiple browsers automatically after deployment, generating detailed execution reports.

## 🛠️ Technology Stack
* **Virtualization & Containers:** oVirt, Docker
* **OS & Security:** Linux, Firewalld
* **Networking & HA:** IPVS, Keepalived (VRRP), Apache
* **Databases:** PostgreSQL, PGPool-II, JavaDB
* **Storage:** GlusterFS (4-node cluster)
* **CI/CD:** Jenkins (Pipelines), Git (GitLab/GitHub), Maven
* **Testing:** Selenide / Selenium
* **App Server:** Payara Server (Java EE)
