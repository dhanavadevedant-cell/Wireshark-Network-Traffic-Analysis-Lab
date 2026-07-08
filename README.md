# 🛡️ Advanced Network Traffic Monitoring & Security Analysis Lab

## 📌 Project Overview
This project documents the design and execution of an isolated network security lab within Oracle VirtualBox. I successfully engineered a Host-Only virtual network topology to monitor, capture, and programmatically analyze live traffic flows between a Kali Linux attack node and a victim system.

The lab focuses on two core cybersecurity disciplines:
1. **Reconnaissance Detection:** Identifying and filtering adversarial port scanning signatures (Nmap SYN Stealth Scans).
2. **Cleartext Credential Harvesting:** Capturing, filtering, and analyzing HTTP password leaks.

---

## 🛠️ Lab Architecture & Tools
* **Hypervisor:** Oracle VirtualBox (Expert Mode)
* **Operating Systems:** Kali Linux (Attacker/Analyst Platform), Windows/Other Target Node
* **Networking Topology:** Isolated Host-Only Private Network (Sandbox)
* **Key Software:** Wireshark v4.x, Nmap v7.x

---

## 🔬 Lab Diary & Analysis Scenarios

### Section 1: Baseline Traffic & Advanced Reconnaissance Detection (Task 1)

#### Initial Baseline Capture
The project began by monitoring routine background chatter on interface `eth0`, establishing normal runtime protocol operations (e.g., LLMNR and ICMPv6).

#### Detecting an Active Port Scan Attack
I simulated an aggressive Nmap SYN Stealth Scan against the target gateway.

**Forensic Findings (The Intrusion Signature):**
Wireshark registered a massive flood of incoming `[SYN]` packets targeting dozens of random destination ports within milliseconds. This rapid-fire sequential probing is the hallmark of a reconnaissance scan. I utilized the Boolean hexadecimal filter `tcp.flags == 0x012` to isolate all `[SYN, ACK]` responses, definitively confirming that the target system was exposed and listening on Port 3306 (MySQL).

#### Evidence Acquisition:
I isolated the target responses using the hex filter. The results confirm that Port 3306 was open and listening for a MySQL database connection, providing an actionable attack vector.

| ![Raw Intrusion Flood](Screenshot 2026-07-08 201103.png) | ![Filtered Open Services](Screenshot 2026-07-07 221449.png) |

---

### Section 2: Cleartext Credential Harvesting (Task 2)

#### Security Recommendation: The Critical Finding
This exercise definitively proved that over an unencrypted HTTP connection (Port 80/8000), user credentials (username and password) are transmitted in plaintext within the request body. Utilizing Wireshark's built-in stream analysis, I successfully reconstructed the conversation and extracted the raw exposed data.

| Protocol | State | Finding | Risk Assessment |
|:---|:---|:---|:---|
| **HTTP** | Unencrypted | **Password Harvested** | **HIGH** |

#### Evidence Acquisition (The Leak):
I instantiated a local mock web server on Port 8000 and simulated a login POST request containing a sample password. By applying the display filter `http.request.method == "POST"` on the loopback interface and following the HTTP stream, I was able to cleanly extract the raw credentials in plaintext:
![Credential Leak Captured](Screenshot 2026-07-08 135723.jpg)
