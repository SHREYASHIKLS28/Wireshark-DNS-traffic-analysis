
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

# Indicators of Compromise Review

The following indicators were evaluated:

- High-entropy subdomains  
- Excessive NXDOMAIN responses  
- Repeated DNS retries  
- Periodic DNS spikes  
- Rogue DNS server usage  
- Large DNS payload anomalies  

**Result:** None were detected.

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
- filters-used.txt (complete filtering methodology)  
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
