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

### Scenario: Detecting Directory Traversal and Automated Scans

I engineered a custom correlation rule to detect automated directory traversal and web scanning activity. Instead of triggering on a single `403 Forbidden` response, this rule looks for multiple occurrences within a short time window, which is a high-fidelity indicator of a web fuzzer or reconnaissance activity.

Specifically, this rule correlates repeated Nginx `403 Forbidden` errors to identify potential automated attempts to access restricted directories or sensitive paths.

---

### Detection Logic

**Base rule triggered:**  
`31101 – Nginx: 403 Forbidden (access denied)`

**Correlation approach:**  
- Monitors repeated 403 errors  
- Threshold: 5 events in 60 seconds  
- Focused on reconnaissance and web scanning behavior  

---

### Custom Wazuh Rule (local_rules.xml)

```xml
<group name="web,appserver,nginx,">
  <rule id="100001" level="10" frequency="5" timeframe="60">
    <if_sid>31101</if_sid>
    <description>
      Nginx: Multiple 403 Forbidden errors detected. Possible automated scan or directory traversal attempt.
    </description>
    <group>web_scan,reconnaissance,</group>
  </rule>
</group>
