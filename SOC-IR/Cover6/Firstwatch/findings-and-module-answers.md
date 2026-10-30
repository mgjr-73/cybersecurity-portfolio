# First Watch Findings and Module Answers
⚠️ The section below contains investigation notes, evidence details, and my answers to the module questions. Expand it only if you're comfortable with spoilers or want to compare your findings.

## Day 1: Reconnaissance (Completed 10/29/26)
<details>
  <summary><strong>Pcap Investigation</strong></summary>
  
  - Found evidence of an automated SYN scan from 67.205.138.164 targeting 104.248.231.191. 
  - Scan revealed ports 80 and 22 were open.
    
</details>

<details>
  <summary><strong>Splunk Investigation</strong></summary>
  
  Query output showed indication of automated web probes from two IPs:
  - 185.220.101.44 - 44 requests on 20 August, 06:00–06:10.
  - 67.205.138.164 - 42 requests on 21 August, 09:00–09:13.
  inconclusive as to which one is the attacker, although both conducted automated probes.
  
</details>

### Module Questions and Answers
<details>
  <summary>Q1. Which address ran the port scan, and which ports did it find open?</summary>
  
  - Based on the pcap investigation, 67.205.138.164 ran the scan and found ports 22 and 80 open.
</details>
<details>
  <summary>Q2. Roughly how fast was the scan? What does that rate tell you about how it was run?</summary>
  
  - Over a thousand packets in nine seconds indicate it was an automated scan.
</details>
<details>
  <summary>Q3. Why does the SYN scan leave no trace in the web log, while the Nmap script probes do?</summary>
  
  - SYN scans do not complete the TCP handshake hence, no connection, no web access log. The Nmap script probes, however, completed the TCP connection and sent HTTP requests, generating web server telemetry.
</details>
<details>
  <summary>Q4. Two sources probed the web server. What would you need to see before calling either of them “the attacker”?</summary>
  
  - We would need additional evidence linking either source to malicious activity before identifying it as the attacker. For example, follow-up requests indicating attempted exploitation, such as SQL injection, command   injection, or path traversal, could provide stronger evidence. At this point, the observed activity supports reconnaissance, but does not establish malicious intent or attribution.
</details>
<details>
  <summary>Q5. If you checked 67.205.138.164 in a threat-intelligence service, what could a result tell you — and what couldn’t it prove?</summary>
  
  - Checking the IP against open-source threat intelligence services like VirusTotal did not show any reports of malicious activity. However, the absence of threat-intelligence reports does not prove that the IP is benign. Further investigation is needed to determine the nature of its activity. An automated port scan and web probe raise some red flags, but the intent remains unclear.
</details>
