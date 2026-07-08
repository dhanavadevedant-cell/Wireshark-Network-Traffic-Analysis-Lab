# 🛡️ Advanced Network Traffic Monitoring & Security Analysis Lab

## 📌 Project Overview
This project documents the design and execution of an isolated network security lab within Oracle VirtualBox. I successfully engineered a Host-Only virtual network topology to monitor, capture, and programmatically analyze live traffic flows between a Kali Linux attack node and a victim system.

The lab focuses on two core cybersecurity disciplines:
1. **Reconnaissance Detection:** Identifying and filtering adversarial port scanning signatures (Nmap SYN Stealth Scans).
2. **Cleartext Credential Harvesting:** Capturing, filtering, and analyzing HTTP password leaks.

---

## 🔬 Lab Diary & Analysis Scenarios

### Section 1: Baseline Traffic & Advanced Reconnaissance Detection (Task 1)

#### Detecting an Active Port Scan Attack
I simulated an aggressive Nmap SYN Stealth Scan against the target gateway. Wireshark registered a massive flood of incoming `[SYN]` packets targeting dozens of random destination ports within milliseconds.

I utilized the Boolean hexadecimal filter `tcp.flags == 0x012` to isolate all `[SYN, ACK]` responses, definitively confirming that the target system was exposed and listening on Port 3306 (MySQL).

#### Evidence Acquisition:

| Nmap Stealth SYN Scan | Isolate Confirmed OPEN Ports |
|---|---|
| ![Raw Intrusion Flood](Screenshot%202026-07-08%20201103.png) | ![Filtered Open Services](Screenshot%202026-07-07%20221449.png) |

This visual data flow confirms that after filtering out ordinary noise, the target system admitted Port 3306 was OPEN and listening for a MySQL database connection, providing the critical vector for a secondary attack.

---

### Section 2: Cleartext Credential Harvesting (Task 2)

#### Security Recommendation: The Critical Finding
This exercise definitively proved that over an unencrypted HTTP connection (Port 80/8000), user credentials (username and password) are transmitted in plaintext within the request body. Utilizing Wireshark's built-in stream analysis, I successfully reconstructed the conversation and extracted the raw exposed data.

#### Evidence Acquisition (The Leak):
I instantiated a local mock web server on Port 8000 and simulated a login POST request containing a sample password. By applying the display filter `http.request.method == "POST"` on the loopback interface and following the HTTP stream, I was able to cleanly extract the raw credentials in plaintext:

![Credential Leak Captured](Screenshot%202026-07-08%20135723.jpg)
