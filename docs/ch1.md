---
layout: default
title: "Chapter 1: Infrastructure & Setup"
nav_order: 2
---

# Chapter 1: Infrastructure and SIEM Deployment

## 1. Objective
The primary goal of this phase was to architect a controlled, isolated virtualization environment to host a Security Operations Center (SOC) ecosystem. This required establishing a reliable telemetry pipeline between a target endpoint and a central management server.

## 2. Environment Architecture
The lab utilizes a three-tier virtualization strategy to simulate a real-world network:

* **SIEM Platform:** Wazuh 4.x (Indexer, Dashboard, and Manager).
* **Target Endpoint:** Ubuntu 22.04 LTS, configured as a production-style server.
* **Offensive Node:** Kali Linux 2024, utilized for adversary simulation.
* **Hypervisor:** Oracle VirtualBox.



## 3. Technical Implementation

### Wazuh Central Stack Deployment
The Wazuh Manager acts as the "brain" of the SOC. I deployed the central stack on a dedicated Linux instance, allocating **4GB RAM** and **2 vCPUs** to ensure the Elasticstack-based indexing engine could process logs without latency.

### Agent Enrollment and Provisioning
To bridge the Ubuntu endpoint to the Manager, I utilized the Wazuh enrollment API. Instead of manual configuration, I executed a scripted deployment:
1.  Generated the deployment command via the Wazuh Dashboard.
2.  Executed the `wget` and `dpkg` sequence on the Ubuntu host.
3.  Configured the agent to communicate over **Ports 1514 (TCP)** and **1515 (TCP)**.

### Network Configuration
I implemented a **Host-Only Adapter** strategy. This provides two critical security benefits:
1.  **Isolation:** Lab traffic (attacks and logs) cannot escape to the physical home network.
2.  **Persistence:** Static IP assignment prevents DHCP lease changes from breaking the Manager-Agent heartbeat.

## 4. Challenges and Troubleshooting
During the initial deployment, the Wazuh Manager experienced significant performance degradation. 
* **Diagnosis:** Monitoring with `htop` and `df -h` revealed high I/O wait times and memory exhaustion.
* **Resolution:** I optimized the VirtualBox settings by enabling **Host I/O Caching** and increasing the RAM overhead. This stabilized the Java-based indexing services.

## 5. SOC Relevance
This phase simulates the **Asset Onboarding** process in a professional SOC. Establishing a reliable log source is the prerequisite for all detection engineering. I successfully validated the "Active" heartbeat, confirming that system logs (syslog/auth.log) were flowing into the SIEM.
