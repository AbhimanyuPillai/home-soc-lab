---
layout: default
title: "Chapter 3: Reconnaissance & Monitoring"
nav_order: 4
---
# Chapter 3: Reconnaissance & Monitoring

## Objective
Detect stealthy reconnaissance attempts and resolve complex virtual networking conflicts.

## Technical Implementation
* **Attacker Tool:** Utilized **Nmap** from Kali Linux to perform Stealth (SYN) scans.
* **Security Tool:** Installed **PSAD (Port Scan Attack Detector)** on the Ubuntu victim.
* **The Logic:** PSAD analyzes `iptables` logs to identify the signature of a port scan, which Wazuh then ingests for alerting.

## Troubleshooting: The "100% Packet Loss" Crisis
During the Nmap simulation, the Kali machine could not "see" the Ubuntu victim despite being on the same subnet.

* **Diagnosis:** VirtualBox Host-Only adapter mismatch.
* **Resolution:** 1. Reconfigured both VMs to use the exact same **Host-Only Ethernet Adapter**.
    2. Assigned static IPs (`192.168.56.x`) to bypass DHCP inconsistencies.
    3. Verified connectivity using a bi-directional `ping` test.

## Detection Results
Once connectivity was restored, an Nmap scan from Kali triggered:
1. **iptables** logged the dropped/rejected packets.
2. **PSAD** analyzed the frequency and flagged it as a scan.
3. **Wazuh** displayed the alert as a reconnaissance event.
