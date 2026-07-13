
# 🛡️ Advanced Network Traffic Monitoring & Security Analysis Lab

## 📌 Project Overview
This project documents the design and execution of an isolated network security lab within Oracle VirtualBox. I successfully engineered a Host-Only virtual network topology to monitor, capture, and programmatically analyze live traffic flows between a Kali Linux attack node and a victim system.

---

## 🔬 Lab Diary & Analysis Scenarios

### Section 1: Advanced Reconnaissance Detection (Task 1)

#### Initial Analysis & Environment Calibration
I initialized the analysis environment by validating the Host-Only interface (`eth0`) configuration and establishing a capture baseline.

![Validating the eth0 Interface](Screenshot%202026-07-08%20130150.jpg)

#### Detecting an Active Port Scan Attack
I simulated an aggressive Nmap SYN Stealth Scan against the target gateway. Wireshark registered a massive flood of incoming `[SYN]` packets targeting dozens of random destination ports within milliseconds. This rapid-fire sequential probing is the hallmark of a reconnaissance scan.

I utilized the Boolean hexadecimal filter `tcp.flags == 0x012` to isolate all `[SYN, ACK]` responses, definitively confirming that the target system was exposed and listening on Port 3306 (MySQL).

#### Evidence Acquisition:
The filtered capture reveals the active, responding network sockets clearly. Confirmed visibility into the victim system's exposed attack surface.

![Filtered Open Services](Screenshot%202026-07-07%20221449.png)

---

### Section 2: Cleartext Credential Harvesting (Task 2)

#### The Interface Diagnostic Challenge
During the simulation of an unencrypted HTTP login attempt, the initial capture bound to the standard network interface (`eth0`) showed zero incoming data packets, verifying that local host-to-host traffic remains isolated from the main virtual adapter.

![Empty Capture on eth0](Screenshot%202026-07-08%20132502.png)

#### The Loopback Pivot & Exploitation Proof
To intercept the localized credentials, I successfully shifted the monitoring capture to the internal **Loopback interface (`lo`)**.

By applying the display filter `http.request.method == "POST"` and selecting **Follow HTTP Stream**, I reconstructed the plaintext communication body, exposing the active credentials entirely unencrypted:

![Credential Leak Captured](Screenshot%202026-07-08%20135723.jpg)

---

## 📈 Portfolio Conclusion & Recommendations
This exercise validates proficiency in protocol identification, manual interface troubleshooting, and forensic analysis. The findings prove that cleartext protocols like HTTP leave user credentials entirely vulnerable to local sniffing. Security baselines must strictly enforce HTTPS/TLS across all target environments.
