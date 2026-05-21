# Cybersecurity Incident Report: TCP SYN Flood Attack Analysis

## 📌 Project Overview
This project focuses on the detection, mitigation, and analysis of a network security incident affecting a travel agency's web server. As a Cybersecurity Analyst, I investigated regular connection timeouts, analyzed color-coded network traffic logs, identified the specific denial-of-service mechanism, and recommended defense actions.

## 🛠️ Tools & Skills Demonstrated
* **Traffic & Log Analysis:** Deep packet inspection interpretation via network logs.
* **Incident Response:** Threat prioritization, identification, and triage escalation.
* **Network Security:** Understanding of core protocols (TCP/IP, HTTP) and security controls (Firewalls, IP Blacklisting).

---

## 📑 Completed Incident Report

### Section 1: Identify the type of attack that may have caused this network interruption

* **One potential explanation for the website's connection timeout error message is:**
  The web server became completely overwhelmed by a massive influx of unauthorized and malicious TCP SYN requests. This rapid exhaustion of the server's resource queue (backlog) left it unable to respond to or process legitimate inbound user traffic, leading directly to connection timeouts (`504 Gateway Time-out`).

* **The logs show that:**
  An unknown and unauthorized external IP address (`203.0.113.0`) is continuously flooding the web server (`192.0.2.1`) with high-volume `[SYN]` packets without completing the connections. Consequently, legitimate employee and client attempts (such as packets 73, 77, and 80 from IPs `198.51.100.16` and `198.51.100.5`) are actively dropping, being forcefully closed with reset flags `[RST, ACK]`, or timing out completely.

* **This event could be:**
  A **TCP SYN Flood Attack**, which is a highly destructive sub-type of **Denial of Service (DoS) / Distributed Denial of Service (DDoS)** attacks.

---

### Section 2: Explain how the attack is causing the website to malfunction

#### 🔄 The Three-Way Handshake Protocol (Normal Behavior)
1. **Step 1 (SYN):** The client sends a `SYN` (Synchronize) packet to the server to establish a connection.
2. **Step 2 (SYN-ACK):** The server allocates localized resources, opens a port, and replies with a `SYN-ACK` (Synchronize-Acknowledgment) packet.
3. **Step 3 (ACK):** The client sends back a final `ACK` (Acknowledgment) packet, completing the handshake to allow safe data transmission.

#### ⚠️ Mechanism of a SYN Flood Attack
During this attack, the malicious actor (`203.0.113.0`) abuses this design by systematically blasting thousands of `SYN` packets towards the server at a rapid rate. 

#### 📈 Log Interpretation & Server Impact
The server responds properly by issuing a `SYN-ACK` to each threat packet and keeping a slot reserved in its connection table. However, because the attacker never sends the final `ACK` packets, these requests are left indefinitely in a **"half-open" state**. 

As seen clearly in the provided data logs, the connection queue backlog is completely filled to maximum capacity. When a legitimate customer tries to browse the sales page (`GET /sales.html`), the server simply has no free memory or TCP slots available. It is forced to drop the request, issue an emergency connection reset (`[RST, ACK]`), or result in a critical downtime failure.

---

## 📊 Project Files & Artifacts
* **[Download Cleaned Analysis Log Sheet](./TCP_Log_Analysis_Report.xlsx):** Contains the formatted, color-coded spreadsheet documenting the exact sequence of network events, attack patterns, and failing requests.

## 🛡️ Recommended Containment & Mitigation
1. **Immediate Action:** Implement temporary firewall rule-blocks targeting the offending IP address (`203.0.113.0`).
2. **Next-Step Hardening:** Enable **TCP SynCookies** on the web server infrastructure to effectively handle tracking overhead without exhausting the connection table backlog.
3. **Infrastructure Defense:** Deploy a Cloud-based Web Application Firewall (WAF) or DDoS protection layer (e.g., Cloudflare) to absorb flood traffic prior to reaching the internal enterprise network.

---
*Note: This repository serves as a practical demonstration of my technical understanding in security operations, threat identification, and network triage.*
