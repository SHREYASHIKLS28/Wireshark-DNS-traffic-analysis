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

**Filter used:**
