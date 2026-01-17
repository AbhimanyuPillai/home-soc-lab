---
layout: default
title: "Chapter 3: Reconnaissance & Monitoring"
nav_order: 4
---

# Chapter 3: Reconnaissance Detection and Monitoring

## 1. Introduction and Objective
Reconnaissance is the foundational stage of the Cyber Kill Chain. Before an adversary launches an exploit, they must map the target’s attack surface to identify open ports, active services, and operating system versions. For a SOC Analyst, detecting this phase is critical; it provides an early warning signal that a threat actor is probing the network, allowing for proactive defense before an actual breach occurs.

This chapter demonstrates the implementation of specialized detection for stealthy port scans. By integrating host-based firewall logging with an intrusion detection tool, we transform a passive server into an active sensor capable of identifying coordinated scanning activity.

## 2. Lab Architecture and Tools Used
The monitoring stack for this phase consists of the following components:

* **Kali Linux (Attacker):** The offensive node used to generate various scanning traffic patterns using Nmap.
* **Ubuntu Server (Target):** The monitored host running the services being probed.
* **iptables:** The host-based firewall used to log unauthorized connection attempts.
* **PSAD (Port Scan Attack Detector):** A lightweight daemon that analyzes iptables logs to detect and characterize scan patterns.
* **Wazuh SIEM:** The central management console used to visualize alerts and aggregate telemetry.

### Why PSAD?
While tools like Suricata provide deep packet inspection (DPI), **PSAD** was chosen for this role because it is highly specialized for port scan detection. It works by analyzing the firewall's log output, making it extremely lightweight and effective at identifying "low and slow" scans that might bypass broader signature-based IDS rules.

## 3. Network Setup
The lab environment utilizes a VirtualBox **Host-Only Network**. This configuration ensures that all traffic remains isolated within the host machine, preventing lab-based attacks from interacting with the physical local area network (LAN).

### Logical Topology


```markdown
### Logical Topology

```text
[ Kali Linux ] <------> [ VirtualBox Host-Only ] <------> [ Ubuntu Server ]
(192.168.56.x)             (Internal Switch)             (192.168.56.y)
