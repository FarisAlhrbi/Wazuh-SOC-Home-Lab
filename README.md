# 🛡️ Wazuh SIEM/XDR: A Home Lab for Security Operations

This repository documents the setup, configuration, and usage of a home-based Security Operations Center (SOC) lab. The project's goal is to gain hands-on experience in threat detection, analysis, and incident response using industry-standard open-source tools.

---

## 🏗️ Lab Architecture

This lab simulates a basic corporate network with a centralized monitoring server and a victim endpoint.

| Component | Technology | Purpose |
|---|---|---|
| **SIEM/XDR** | Wazuh Server 4.x.x | Centralized log collection, analysis, and threat detection. |
| **Endpoint** | Windows 10 Enterprise | The "victim" machine generating security logs. |
| **Virtualization** | Oracle VirtualBox | Platform for running all virtual machines. |
| **Networking** | VirtualBox Host-Only & NAT | Isolated network for secure testing with internet access. |

![Lab Diagram](https://i.imgur.com/YOUR_DIAGRAM_URL.png)  <-- (سنضيف صورة لاحقًا)

---

## ⚔️ Simulated Attacks & Detections

Here are some of the attack scenarios simulated in this lab and how they were detected by Wazuh.

### 1. Unauthorized User Creation

*   **Attack:** A new local user named "hacker" was created on the Windows 10 machine using PowerShell.
*   **Detection:** Wazuh generated a high-severity alert (Rule ID: 60103) for "User added to the local Windows machine."
*   **Analysis:** The alert details showed the target user (`hacker`), the user who performed the action (`vagrant`), and the exact time of the event.

**(Screenshot of the Wazuh alert will be here)**

---

## 💡 Key Skills & Learnings

*   **Threat Detection & Analysis**
*   **Log Management & Correlation**
*   **Vulnerability Assessment**
*   **Incident Response Fundamentals**
*   **Network Troubleshooting**
*
