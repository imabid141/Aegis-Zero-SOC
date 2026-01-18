![Status](https://img.shields.io/badge/Status-Phase_1_Complete-green?style=for-the-badge&logo=checkmarx)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge&logo=opensourceinitiative)
![Platform](https://img.shields.io/badge/Platform-Docker-blue?style=for-the-badge&logo=docker)
![Role](https://img.shields.io/badge/Role-SOC_Engineering-red?style=for-the-badge&logo=kali-linux)

# Project Aegis-Zero: Autonomous Agentic SOC

## Phase 1: High-Efficiency SIEM Foundation

**Author:** Ghulam Muhammad  
**Status:** Phase 1 (Core Infrastructure) Complete  
**Architecture:** Containerized Microservices  
**Hardware Target:** Low-Resource Local Hardware (16GB RAM / 4-Core CPU)

---

## 1. Project Vision

Modern Security Operations Centers (SOCs) face "Alert Fatigue" and high infrastructure costs. **Aegis-Zero** is a proof-of-concept designed to prove that enterprise-grade security monitoring can be:

1. **Cost-Effective:** $0 infrastructure cost using Open Source tools.
2. **Resource-Efficient:** Custom-tuned to run on consumer-grade hardware.
3. **Autonomous:** Future phases include LangGraph AI Agents for automated triage.

## 2. Technical Achievement (Phase 1)

In this phase, I deployed a fully functional **Wazuh SIEM** stack (Indexer, Manager, and Dashboard) using Docker.

### Key Engineering Challenges Overcome

* **Memory Optimization:** Successfully tuned the Wazuh Indexer (Java-based OpenSearch) from the standard 4GB allocation down to **1.5GB (Xms1500m/Xmx1500m)**. This ensures stability on a 16GB host while leaving overhead for upcoming AI modules.
* **Security Hardening:** Implemented full TLS/SSL certificate-based communication between all microservices using a custom Certificate Authority (CA).
* **Docker Orchestration:** Utilized Docker Compose with hard resource limits (`deploy: resources: limits`) to prevent system-wide crashes during log spikes.
* **Kernel Tuning:** Optimized Linux host kernel parameters (`vm.max_map_count`) to support high-performance indexing.

## 3. The Stack

| Service | Role | Memory Limit | Status |
| :--- | :--- | :--- | :--- |
| **Wazuh Indexer** | Log Storage & Search | 2.0 GB | Healthy |
| **Wazuh Manager** | Analysis & Rule Engine | 2.0 GB | Healthy |
| **Wazuh Dashboard**| UI & Visualization | 2.0 GB | Healthy |

## 4. Deployment Guide (Zero to Operational)

Follow these exact steps to deploy the Aegis-Zero stack on a fresh Linux host (Ubuntu/Kali).

### Step 1: Clone and Navigate

```bash
git clone https://github.com/imabid141/Aegis-Zero-SOC.git
cd Aegis-Zero-SOC/core-stack

```

### Step 2: Optimize Linux Kernel (Required for Indexer)

The Wazuh Indexer (OpenSearch) requires the host's memory map limits to be increased, or the container will fail to start.

```bash
# Set limit for the current session
sudo sysctl -w vm.max_map_count=262144

# Make the change permanent across reboots
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf

```

### Step 3: Generate Security Certificates

Initialize the SSL/TLS certificates for secure communication between the Indexer, Manager, and Dashboard.

```bash
docker compose -f generate-indexer-certs.yml run --rm generator

```

### Step 4: Launch the Microservices

Start the stack in detached mode.

```bash
docker compose up -d

```

### Step 5: Verification

Wait approximately 30 seconds (first time it took few mints) for the Java engine to initialize, then verify the cluster health:

```bash
# Check container status
docker ps

# Verify Indexer API Authentication
curl -k -u admin:SecretPassword https://localhost:9200

```

## 5. Next Milestones

* [ ] **Phase 2:** Integrate Suricata for Network Intrusion Detection (NIDS).
* [ ] **Phase 3:** Deploy ML-based anomaly detection for "Slow and Low" network scans.
* [ ] **Phase 4:** Integrate LangGraph AI Agent for autonomous alert investigation.

## 6. Proof of Deployment (Phase 1)

### A. Container Health (Docker)

All microservices are running in a health state with strict memory limits.
![Docker Status](./docs/screenshots/docker_ps.png)

### B. Indexer API Verification

Secure communication with the Wazuh Indexer verified via authenticated CLI.
![Indexer Verification](./docs/screenshots/indexer_verify.png)

### C. Live Security Dashboard

The Web UI is fully operational, showing active event monitoring.
![Wazuh Dashboard](./docs/screenshots/dashboard.png)
