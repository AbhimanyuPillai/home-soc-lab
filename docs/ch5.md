---
layout: default
title: "Chapter 5: The Alerting Pipeline & FIM"
nav_order: 6
---

# Chapter 5: The Alerting Pipeline & Advanced FIM 📬

This phase of the lab was dedicated to closing the gap between detection and notification. While the SIEM dashboard provides excellent visibility, a SOC analyst cannot monitor a browser 24/7. I engineered a real-time alerting pipeline to push critical security events directly to Gmail, ensuring immediate visibility into high-severity threats.

---

## 🏗️ Architecture: The Notification Loop

To enable external notifications, I integrated a mail transfer agent (MTA) into the Wazuh Manager. The architecture follows this flow:
1. **Wazuh Manager** identifies a high-severity event (Level 10+ or specific FIM rules).
2. **Postfix** acts as a relay, passing the alert to Google’s SMTP servers via an encrypted tunnel.
3. **Gmail** delivers the push notification to my mobile device.

### ⚙️ Postfix Relay Configuration
The primary challenge was ensuring secure communication between the Manager and the SMTP relay.
* **Component:** Postfix MTA.
* **Security:** Configured SASL authentication and TLS encryption to satisfy Google’s security requirements.
* **App Passwords:** Since modern SMTP relays block standard passwords, I utilized a dedicated Google App Password to maintain the integrity of the relay.

---

## 🔍 Advanced File Integrity Monitoring (FIM)

Standard FIM simply reports that a change occurred. To achieve "Forensic Maturity," I upgraded the lab to use the **`whodata` engine**.

### Technical Implementation:
* **The Audit Subsystem:** By installing `auditd` on the Ubuntu agent and configuring Wazuh to use `whodata="yes"`, the SIEM now hooks into the Linux kernel.
* **Identity Attribution:** This allows the lab to capture the specific **User ID (uid)** and the **Process Name** (e.g., `touch`, `nano`, `tee`) responsible for every file modification.

### Critical Asset Protection:
I established real-time "laser tripwires" on the most sensitive files in the operating system:
1. **`/etc/shadow`**: Monitoring for unauthorized password tampering.
2. **`/root` directory**: Watching for the creation of unauthorized backdoors or scripts.

---

## 🛠️ Challenges & Troubleshooting

### 1. The Sudo Redirection Trap
**The Problem:** While testing FIM, running `sudo echo "text" >> /root/file` resulted in a "Permission Denied" error. 
**The Cause:** In Linux, the `sudo` only applies to the `echo` command, while the shell attempting the redirection (`>>`) still runs as a low-privileged user.
**The Solution:** I utilized the `tee` command: `echo "data" | sudo tee -a /root/file`. This allows the piped data to be written to a root-owned file with elevated permissions.

### 2. Taming Alert Fatigue (Granular Tuning)
**The Problem:** Default Wazuh settings only email for Level 12+ alerts. Most FIM events (like file modifications) are Level 7, meaning they wouldn't trigger an email.
**The Solution:** I engineered a "VIP Pass" system in the `ossec.conf` file. By creating a specific `<email_alerts>` block for Rule IDs 550, 553, and 554, I ensured that critical file changes trigger a Gmail notification even if they fall below the global severity threshold.

---

## 🧪 Simulation & Results

### Case 1: Brute-Force Notification
Launching a high-velocity attack via Hydra successfully triggered a Level 10 alert. Within seconds, the mailing pipeline dispatched the log details, providing the attacking IP and the targeted user account directly to my inbox.

### Case 2: The "Shadow" Tripwire
I simulated a password vault modification by updating the metadata of `/etc/shadow`. 
* **Detection:** The Wazuh FIM module identified the change in real-time.
* **Notification:** Because of the custom rule tuning, the manager bypassed the global threshold and sent an instant high-priority email.
* **Forensics:** The alert successfully identified the `root` user as the actor, proving that the `whodata` engine was functioning correctly.

---

**Next Steps:** With the notification pipeline established, I am moving toward "Active Response Phase 2"—automatically isolating compromised hosts based on these FIM triggers.
