---
layout: default
title: "Chapter 2: Web Defense & NIDS"
nav_order: 3
---

# Chapter 2: Web Defense and Custom Rule Engineering

## 1. Objective
This phase focuses on expanding the monitoring surface by deploying a web server and a Network Intrusion Detection System (NIDS). The goal was to practice **Detection Engineering** by writing custom XML rules to identify specific web-based attack patterns.

## 2. Technical Implementation

### Web Surface Deployment
I installed and hardened **Nginx** on the Ubuntu endpoint to simulate a public-facing web application. This provides a rich source of application-layer logs (`access.log` and `error.log`).

### NIDS Integration (Suricata)
I deployed **Suricata** to monitor network traffic at the packet level.
* **Configuration:** Configured Suricata to run in "Promiscuous Mode" on the primary interface.
* **Telemetry Pipeline:** Linked Suricata's `eve.json` output to the Wazuh agent. This allows for correlation between network alerts (e.g., SQL injection signatures) and system-level events.

## 3. Detection Engineering: Custom XML Rules
Standard SIEM rules are often too broad. To improve visibility, I manually edited the Wazuh Manager's `local_rules.xml` to create environment-specific alerts.

### Scenario: Detecting Directory Traversal / Scans
I engineered a rule to detect a high frequency of **403 Forbidden** errors. This pattern often indicates an automated fuzzer or an attacker attempting to access restricted directories.

```xml
<group name="web,appserver,nginx,">
  <rule id="100001" level="10">
    <if_sid>31101</if_sid>
    <description>Nginx: High frequency of 403 Forbidden errors (Potential Web Scan)</description>
  </rule>
</group>
