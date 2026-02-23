# DNS Traffic Analysis Using Wireshark

## Project Type
**Network Traffic Investigation – Blue Team / SOC-Focused Analysis**

---

## Overview

This project presents a structured DNS traffic investigation conducted using Wireshark. The objective was to analyze a captured packet file and identify potential malicious behavior including:

- DNS tunneling  
- Domain Generation Algorithm (DGA) activity  
- Beaconing communication  
- Abnormal DNS failures  
- Data exfiltration patterns  

The investigation follows a layered methodology similar to SOC Tier-1 analysis workflows.

---

## Capture Details

- **PCAP File:** `Case1_Baseline_Web_Traffic.pcapng`
- **Total Packets:** 53,944
- **Tool Used:** Wireshark
- **Capture Interface:** Wi-Fi
- **Client IP:** 172.17.84.79
- **DNS Server:** 172.17.80.1

---

## Investigation Objectives

The analysis aimed to detect:

- DNS tunneling via long encoded subdomains  
- DGA-generated random domain patterns  
- High NXDOMAIN or SERVFAIL error rates  
- Beaconing or fixed-interval DNS communication  
- Rogue DNS server usage  
- DNS-based data exfiltration  

---

# Investigation Methodology

---

## 1. DNS Traffic Isolation

**Filter used:** dns

**Purpose:**  
Isolate all DNS traffic for inspection.

**Findings:**

- All queries directed to internal DNS resolver (172.17.80.1)
- Destination port consistently 53
- Domains associated with known vendors (Google, Microsoft, advertising infrastructure)
- No rogue DNS server usage detected

---

## 2. Removal of Multicast Noise

**Filter used:** dns && !(mdns)

**Purpose:**  
Remove local multicast DNS traffic to eliminate false positives.

**Findings:**

- Only standard DNS traffic remained
- No abnormal broadcast amplification detected

---

## 3. Long Domain Name Analysis (Tunneling Detection)

**Filter used:** dns.qry.name.len > 40

**Purpose:**  
Detect potential DNS tunneling through long or encoded subdomains.

**Findings:**

- Long domains observed were structured and vendor-associated
- No base64-like or high-entropy subdomains
- No repeated encoded patterns
- No evidence of DNS tunneling

---

## 4. DNS Error Code Analysis (DGA Detection)

**Filter used:** dns.flags.rcode != 0

**Purpose:**  
Identify abnormal NXDOMAIN or SERVFAIL patterns.

**Findings:**

- Limited NXDOMAIN responses
- Limited SERVFAIL responses
- Errors associated with advertising/tracking domains
- No repeated random domain failures
- No DGA behavior detected

---

## 5. UDP Conversation Analysis

**Tool used:**  
Statistics → Conversations → UDP

**Findings:**

- Each DNS conversation consisted of one query and one response
- No excessive packet counts
- No retry bursts
- No large payload anomalies
- No beaconing or tunneling pattern detected

---

## 6. Time-Based Traffic Analysis

**Tool used:**  
Statistics → I/O Graph (1-second interval)

**Findings:**

- Traffic spikes correlated with manual browsing
- No fixed-interval periodic communication
- DNS activity reduced during idle periods
- Minimal TCP retransmissions
- No persistent C2-style communication observed

---

# Indicators of Compromise (IOC) Review

The following indicators were evaluated:

- High-entropy subdomains  
- Excessive NXDOMAIN responses  
- Repeated DNS retries  
- Periodic DNS spikes  
- Rogue DNS server usage  
- Large DNS payload anomalies  

**Result:**  
None were detected.

---

# Final Assessment

The analyzed packet capture represents normal baseline web browsing and background system activity.

No evidence of:

- DNS tunneling  
- DGA-based malware  
- Command-and-Control communication  
- DNS-based data exfiltration  
- Beaconing behavior  

The investigation confirms legitimate DNS resolution behavior consistent with standard browsing patterns.

---

# Artifacts Included

- Screenshots of each analysis phase  
- `filters-used.txt` (complete filtering methodology)  
- Executive summary (PDF format)  

---

# Skills Demonstrated

- Protocol-level traffic isolation  
- DNS tunneling detection methodology  
- DGA detection logic  
- Error code analysis  
- Conversation-level inspection  
- Time-based behavioral analysis  
- False-positive elimination  

---

## Author

Blue Team DNS Investigation Project  
SOC-Focused Network Traffic Analysis  
