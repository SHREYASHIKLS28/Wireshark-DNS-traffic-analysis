
#  Case 1 – DNS Traffic Analysis Using Wireshark

##  Overview

This case study analyzes DNS traffic from a captured network packet file using Wireshark.
The objective was to detect potential malicious indicators such as DNS tunneling, DGA activity, beaconing behavior, and abnormal DNS resolution patterns.

---

##  File Details

* **PCAP File:** `Case1_Baseline_Web_Traffic.pcapng`
* **Tool Used:** Wireshark (v4.x)
* **Interface Captured:** Wi-Fi
* **Total Packets Captured:** 53,944
* **Client IP:** 172.17.84.79
* **DNS Server:** 172.17.80.1

---

#  Investigation Objectives

The analysis focused on identifying:

* DNS tunneling
* Domain Generation Algorithm (DGA) activity
* Beaconing behavior
* Excessive NXDOMAIN responses
* Rogue DNS usage
* Data exfiltration via DNS
* Abnormal traffic patterns

---

#  Investigation Phases

---

##  Phase 1 – DNS Traffic Isolation

### Filter Used

```wireshark
dns
```

### Malicious Indicators Checked

* Excessive DNS query volume
* Queries to unauthorized DNS servers
* Non-standard DNS ports
* Suspicious top-level domains (.xyz, .top, etc.)

### Observations

* All DNS traffic directed to internal resolver `172.17.80.1`
* Destination port consistently `53`
* Domains observed:

  * Google services
  * Microsoft services
  * Advertising/analytics domains

### Conclusion

DNS flow followed expected client → internal DNS server pattern.
No rogue resolver or abnormal DNS behavior detected.

---

##  Phase 2 – Removal of Multicast Noise (MDNS Filtering)

### Filter Used

```wireshark
dns && !(mdns)
```

### Purpose

To remove multicast DNS traffic such as:

* Apple AirPlay
* Printer discovery
* Local device broadcasts

### Why This Matters

Multicast DNS can generate false positives and obscure meaningful DNS analysis.

### Observations

* Only standard DNS (port 53) traffic remained.
* No abnormal broadcast amplification detected.

### Conclusion

Noise successfully removed. Clean dataset prepared for deeper analysis.

---

##  Phase 3 – Long Domain Name Analysis (DNS Tunneling Detection)

### Filter Used

```wireshark
dns && !(mdns) && dns.qry.name.len > 40
```

### Malicious Indicators Checked

* Long, high-entropy subdomains
* Base64-like encoded strings
* Repeated long DNS queries
* Structured random character sequences

### Observations

Examples of long domains observed:

* `prod.tahoe-analytics.publishers.advertising.a2z.com`
* `safeframe.googlesyndication.com`

Characteristics:

* Structured subdomains
* Recognizable vendor infrastructure
* No high-entropy randomness
* No encoded payload segments
* No repeated pattern

### What DNS Tunneling Typically Looks Like

```
ajsdhaskjdhaskjdhaskjdh.maliciousdomain.com
```

or

```
ZXhhbXBsZWRhdGE=.evil.com
```

### Conclusion

No evidence of DNS tunneling or encoded data exfiltration observed.

---

##  Phase 4 – DNS Error Code Analysis (DGA Detection)

### Filter Used

```wireshark
dns.flags.rcode != 0
```

### Malicious Indicators Checked

* High NXDOMAIN frequency
* Continuous SERVFAIL responses
* Repeated failed queries to random domains
* Retry loops for same domain

### Observations

* Limited NXDOMAIN responses
* Limited SERVFAIL responses
* Failures linked to advertising/tracking domains
* No repeated high-frequency failure pattern

### Why This Matters

DGA malware produces:

* Large volumes of random domain queries
* High NXDOMAIN rates
* Repeated failed resolution attempts

This pattern was not observed.

### Conclusion

DNS error rate consistent with normal web browsing behavior.

---

##  Phase 5 – UDP Conversation Analysis

**Tool Used:**
Statistics → Conversations → UDP

### Malicious Indicators Checked

* High packet count per DNS conversation
* Repeated queries without responses
* Large DNS payload sizes
* Multiple retries per request

### Observations

* Each DNS conversation contained:

  * 1 query
  * 1 response
* Total packets per conversation: 2
* No abnormal payload size
* No retry burst behavior

### What Beaconing Looks Like

```
Query → No response → Retry → Retry → Retry
```

### Conclusion

All DNS conversations followed normal resolution behavior.

---

##  Phase 6 – Time-Based Traffic Analysis

**Tool Used:**
Statistics → I/O Graph
Interval: 1 second

### Malicious Indicators Checked

* Fixed-interval traffic spikes
* Sustained outbound traffic
* Continuous DNS traffic independent of user activity
* High retransmission rates

### Observations

* Traffic spikes aligned with browsing activity
* No evenly spaced periodic spikes
* DNS traffic reduced during idle periods
* Minimal TCP retransmissions

### What Malware Beaconing Looks Like

Regular spikes every 5–10 seconds regardless of user behavior.

### Conclusion

Traffic pattern consistent with manual user-driven browsing.

---

#  Consolidated Malicious Indicator Review

| Indicator                      | Detected | Verdict      |
| ------------------------------ | -------- | ------------ |
| DNS tunneling                  | ❌        | Not observed |
| DGA domains                    | ❌        | Not observed |
| Excessive NXDOMAIN             | ❌        | Not observed |
| Periodic beaconing             | ❌        | Not observed |
| Rogue DNS resolver             | ❌        | Not observed |
| Abnormal DNS payload size      | ❌        | Not observed |
| Sustained exfiltration pattern | ❌        | Not observed |

---

#  Final Verdict

The analyzed PCAP file represents baseline web browsing and normal background system activity.

There is no evidence of:

* DNS-based data exfiltration
* Command-and-Control (C2) communication
* Domain Generation Algorithm (DGA) activity
* DNS tunneling
* Beaconing behavior

Traffic behavior, resolution patterns, conversation statistics, and time-based analysis all align with expected legitimate activity.

---

#  Skills Demonstrated

* Protocol isolation and filtering
* Noise elimination (MDNS removal)
* DNS tunneling detection methodology
* DGA detection logic
* DNS error analysis
* Conversation-level inspection
* Time-based behavioral analysis
* False-positive elimination

