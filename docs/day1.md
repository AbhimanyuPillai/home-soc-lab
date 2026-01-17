# Day 1: Infrastructure & SIEM Deployment

## Objective
Establish the primary communication link between the Wazuh Manager and the Ubuntu Endpoint.

## Setup Details
* **Hypervisor:** VirtualBox
* **Wazuh Version:** 4.x
* **Agent OS:** Ubuntu 22.04 LTS

## Technical Steps
1. **Manager Installation:** Deployed the Wazuh central stack. Verified the Indexer and Dashboard services were active.
2. **Agent Enrollment:** Generated a deployment key via the Wazuh Dashboard.
3. **Connectivity Test:** Ran the installation script on the Ubuntu machine.
   ```bash
   # Example of the enrollment command
   curl -so wazuh-agent.deb [https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/](https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/)...
