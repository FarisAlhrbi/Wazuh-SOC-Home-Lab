<img width="2550" height="1394" alt="Screenshot 2025-10-28 191228" src="https://github.com/user-attachments/assets/fc9859c0-a265-4e24-a0ea-127217be230e" /># SOC Home Lab Project: Threat Analysis with a Wazuh SIEM

## 🚀 Introduction

This project documents the end-to-end creation of a fully functional Security Operations Center (SOC) home lab. Built from the ground up, this lab serves as a practical environment to simulate, detect, and analyze common cyber threats. The primary goal was to bridge the gap between theoretical knowledge (from certifications like CompTIA Security+ and Cisco CCNA) and the hands-on, practical skills required for a SOC Analyst (Tier 1) role.

The project covers the complete threat lifecycle: from infrastructure architecture and network engineering to executing a controlled attack, detecting it with a SIEM, and analyzing the results.

---

## 🎯 Key Project Objectives

*   **Gain Practical SIEM Experience:** Deploy, configure, and manage a Wazuh SIEM solution for centralized log collection and security event analysis.
*   **Master Secure Network Engineering:** Design and implement a segmented network architecture to isolate critical assets and control traffic flow.
*   **Simulate Red Team vs. Blue Team Scenarios:** Execute realistic attack techniques (Red Team) and then pivot to a defensive stance to detect and analyze the intrusion (Blue Team).
*   **Develop a Strong Professional Portfolio:** Use this project as a tangible demonstration of technical skills for resumes, LinkedIn, and technical interviews.
*   **Deepen Troubleshooting Skills:** Overcome real-world technical challenges, including performance bottlenecks, network policy conflicts, and tool configuration issues.

---

## 🛠️ Lab Architecture

The lab was built on **VMware Workstation Pro** to ensure high performance and stability, after initially diagnosing and resolving critical I/O performance issues encountered with VirtualBox.

### Network Design

To mirror a corporate environment and enforce a "defense-in-depth" strategy, a segmented network was designed using VMware's Virtual Network Editor. This isolates the secure management network from the operational network where attacks are simulated.

*   **Management Network (`VMnet2`):**
    *   **Subnet:** `192.168.56.0/24`
    *   **Description:** A secure, isolated network for managing servers and accessing the Wazuh dashboard.
    *   **Assets:** Wazuh Server (Management Interface), Analyst Workstation.

*   **Operations & Logging Network (`VMnet3`):**
    *   **Subnet:** `192.168.170.0/24`
    *   **Description:** The network for operational endpoints and the attacker machine. It also serves as the channel for agents to forward logs to the Wazuh server's collector interface.
    *   **Assets:** Wazuh Server (Log Collector Interface), Attacker Machine, Victim Machine.

### Asset Inventory

| Asset Role | Operating System | IP Address | Network (VMnet) | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Wazuh Server** | Ubuntu 24.04 | `192.168.56.4` <br> `192.168.170.10` | `VMnet2` (Mgmt) <br> `VMnet3` (Logs) | SIEM & Log Collector |
| **Analyst Station** | Windows 10 | `192.168.56.131` | `VMnet2` (Mgmt) | The SOC Analyst's workstation |
| **Attacker Machine**| Kali Linux | `192.168.170.128` | `VMnet3` (Ops) | Simulates the Threat Actor |
| **Victim Machine** | Windows 10 | `192.168.170.129` | `VMnet3` (Ops) | The target endpoint |

---

## 🛡️ Attack & Analysis Scenarios

Two primary scenarios were executed to test the lab's detection and analysis capabilities.

### Scenario 1: Brute-Force Attack Against a Hardened System

This scenario simulates a noisy, unsophisticated attacker attempting to breach a properly configured Windows endpoint.

**1. The Attack (Red Team):**
*   A dictionary-based brute-force attack was launched from the Kali machine using `Hydra` against the victim's RDP service.
*   The attack failed as the target system began refusing connections after a small number of attempts.

<img width="2541" height="1392" alt="Screenshot 2025-10-28 190704" src="https://github.com/user-attachments/assets/016ab4c3-80a0-4065-b8d6-999ae53b59c8" />


**2. The Analysis (Blue Team):**
*   Investigation revealed that the attack was thwarted by a built-in Windows security policy.
*   The system's **Account Lockout Policy** was triggered, locking the target account and preventing any further login attempts. This is a critical defense mechanism against brute-force attacks.
*   The lockout was confirmed by inspecting the user account properties on the victim machine.

<img width="2543" height="1400" alt="Screenshot 2025-10-28 190727" src="https://github.com/user-attachments/assets/1d131bb9-d9ea-4442-9882-db4cab50ce9d" />


**Conclusion:** This demonstrates the effectiveness of modern OS hardening and the importance of proactive security controls. The lab successfully simulated an attack and validated a key defensive measure.

---

### Scenario 2: Full-Cycle Attack Analysis in a Lab Environment

For deeper analysis, the Account Lockout Policy was temporarily disabled to observe the full lifecycle of a successful brute-force attack.

**1. Environment Modification:**
*   The `Account lockout threshold` was set to `0` in the Local Group Policy Editor, intentionally weakening the system for educational purposes.

<img width="2550" height="1394" alt="Screenshot 2025-10-28 191228" src="https://github.com/user-attachments/assets/6b910e4a-ae92-407e-a54b-7a0fb5c0f0ea" />


**2. The Attack (Red Team):**
*   The `Hydra` attack was re-launched. This time, with the lockout policy disabled, the attack was successful in guessing the correct password.

<img width="2543" height="1400" alt="Screenshot 2025-10-28 193455" src="https://github.com/user-attachments/assets/1d56c554-0b8e-4256-8093-82381786bf6d" />


**3. The Detection (Blue Team):**
*   Wazuh successfully detected and logged the entire attack chain:
    1.  **Initial Failed Logons:** A series of `Windows User Logoff` alerts were generated for the failed attempts.
    2.  **Successful Logon:** Once the password was cracked, Wazuh immediately triggered a high-priority alert for a successful remote desktop logon.
*   **Relevant Wazuh Rules Triggered:**
    *   `Rule 60137`: Windows User Logoff (indicating failed attempts)
    *   `Rule 60204`: User logon using Remote Desktop Connection


<img width="2548" height="1390" alt="Screenshot 2025-10-28 195812" src="https://github.com/user-attachments/assets/9d866eab-b003-4417-8666-e5a7c615ed0e" />


**4. Post-Exploitation:**
*   Using the compromised credentials, a successful RDP connection was established from the Kali machine to the victim, confirming full system access.

<img width="2546" height="1386" alt="Screenshot 2025-10-28 195736" src="https://github.com/user-attachments/assets/d7697185-b127-4819-a4d8-a4ee3e84ce12" />
<img width="2550" height="1358" alt="Screenshot 2025-10-28 190848" src="https://github.com/user-attachments/assets/7c3bfd5b-19f0-4b64-9d98-ab215f4f852c" />


**Conclusion:** This scenario validates the lab's ability to provide full visibility into an attack chain, from initial attempts to successful compromise, showcasing the SIEM's critical role in threat detection.

---

## 🎓 Skills & Lessons Learned

*   **SIEM Deployment & Management:** Deployed and configured Wazuh, enrolled agents, analyzed alerts, and interpreted correlation rules.
*   **Network Engineering:** Designed and implemented isolated virtual networks, configured multiple network interfaces on Linux, and managed firewall rules.
*   **Advanced Troubleshooting:** Diagnosed and resolved complex issues related to VM performance (I/O), network connectivity (RDP, ICMP), and security policy conflicts (Account Lockout, NLA).
*   **OS Hardening & Security Policy:** Gained hands-on experience with critical Windows security policies like Account Lockout and User Rights Assignment.
*   **Penetration Testing Tools:** Utilized `Hydra`, `Nmap`, and `xfreerdp` to simulate attacker TTPs (Tactics, Techniques, and Procedures).
*   **SOC Analyst Mindset:** Developed the ability to correlate disparate events, understand the context behind alerts, and reconstruct an attack timeline.
