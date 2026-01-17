---
layout: default
title: Day 2 - Custom Rules
nav_order: 2
---
# Day 2: Web Security & Custom Rule Engineering

## Objective
Simulate a production web environment and engineer custom detection rules for non-standard security events.

## Technical Implementation
* **Web Server:** Installed **Nginx** on the Ubuntu endpoint.
* **Network IDS:** Deployed **Suricata** to monitor network traffic.
* **Log Integration:** Pointed the Wazuh agent to monitor Suricata’s `eve.json` and Nginx's `access.log`.

## Custom Rule Engineering (XML)
Standard SIEM rules often miss specific environmental nuances. I manually updated the Wazuh Manager's ruleset to escalate the visibility of web-based threats.

### Why Custom Rules?
Default rules might categorize a failed login or a directory traversal attempt as a "low-level" event. I wrote custom XML logic to "boost" these to **Level 10+** to ensure they appear on the main dashboard as high-priority alerts.

### XML Configuration Snippet:
```xml
<group name="web,appserver,nginx,">
  <rule id="100001" level="10">
    <if_sid>31101</if_sid>
    <description>Nginx: High frequency of 403 Forbidden errors (Possible Web Scan)</description>
  </rule>
</group>
