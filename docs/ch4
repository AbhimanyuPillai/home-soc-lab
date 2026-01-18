---
layout: default
title: "Chapter 4: Adversary Simulation & Active Response"
nav_order: 5
---

# Chapter 4: Adversary Simulation and Active Response

## 1. Objective
The goal of this phase was to transition from a passive monitoring state to an active defensive posture. By simulating real-world attack vectors, I tested the SIEM's ability to not only detect high-severity threats but to automatically neutralize them using Wazuh’s **Active Response** capabilities.

## 2. Red Team: Adversary Simulation
To test the defenses, I utilized **Kali Linux** as an external attacker node within the isolated lab network.

### Brute Force Attack (SSH)
I performed a high-velocity credential-stuffing attack using **Hydra**. The intent was to simulate an attacker attempting to gain unauthorized access via the SSH protocol.
* **Tool:** Hydra
* **Target:** Ubuntu Server (192.168.56.102)
* **Wordlist:** /usr/share/wordlists/rockyou.txt

### Reconnaissance (Nmap)
I conducted stealthy and aggressive scans to map the target's open ports and services, specifically looking for vulnerabilities in the Nginx and SSH configurations.



## 3. Blue Team: Detection and Remediation
The Wazuh SIEM was configured to monitor system authentication logs and network traffic for anomalies.

### Alert Escalation
* **Brute Force Detection:** The high frequency of failed login attempts triggered **Rule 5712 (SSHD Brute Force)**, which I escalated to a **Level 10 (Critical)** alert.
* **Scan Detection:** The Nmap probes triggered **Level 12** alerts, identifying the Kali Linux IP as a hostile source.

### Automated Active Response
Instead of manual intervention, I engineered an automated response to drop packets from the offending IP.
* **Logic:** If an IP triggers a Brute Force alert more than 5 times within a 60-second window, the Wazuh Manager sends an instruction to the Ubuntu Agent.
* **Action:** The agent executes a `firewall-drop` script, adding the attacking IP to the local `iptables` drop list for a duration of **180 seconds (3 minutes)**.

## 4. Technical Implementation (local_rules.xml)
Below is the custom logic used to correlate multiple failed attempts into a single actionable event:

{% highlight xml %}
<group name="syslog,sshd,">
  <rule id="100002" level="10" frequency="5" timeframe="60">
    <if_sid>5712</if_sid>
    <description>SSHD: Multiple failed logins. Triggering Automated IP Block.</description>
    <group>authentication_failed,pci_dss_10.2.4,</group>
  </rule>
</group>
{% endhighlight %}

## 5. Challenges and Troubleshooting
* **The "Management Lockout" Risk:** During initial testing, the Active Response script accidentally targeted the wrong interface, which risked blocking my own management workstation.
* **The Fix:** I refined the `ossec.conf` on the manager to ensure the response was restricted to specific rule IDs and validated that the whitelisting for my admin IP was functioning correctly.



## 6. SOC Relevance
This phase demonstrates a "Mature" SOC capability. In a production environment, the time-to-respond is critical. By automating the containment of an attacker, we effectively "buy time" for a human analyst to perform a deeper investigation while the immediate threat is neutralized.
