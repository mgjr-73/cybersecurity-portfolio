# First Watch Findings and Module Answers
#### ⚠️ WARNING: The section below contains investigation notes, evidence details, and my answers to the module questions. Expand it only if you're comfortable with spoilers or want to compare your findings.

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


## Day 2: Web Enumeration (Completed 11/4/26)
<details>
  <summary><strong>Pcap Investigation</strong></summary>
  
  - **Top talkers:** `67.205.138.164` (external source IP) → `104.248.231.191` (Odapeeka web server).
    
  - **Automated web enumeration:** Initial analysis using the `http.request` filter revealed activity consistent with automated web enumeration. The source generated 45,896 HTTP requests within approximately five seconds. The requested paths included common files, directories, and file extensions, suggesting the use of a wordlist containing commonly targeted filenames and paths.
    
  - **Tool identification:** The HTTP requests identified `gobuster/3.8.2` in the User-Agent header, providing further evidence of automated directory and file enumeration.
    
  - **robots.txt discovery:** The server's `robots.txt` file revealed `/staff`, `/backup`, `/config`, `/logs`, and `/admin` in its Disallow list. These paths provided the scanner with additional targets to investigate.
    
  - **Information disclosure through index.html:** Inspection of the homepage's HTML source revealed internal information, including the location of `db.conf`, the organization's CMS version, internal development notes, and a message from an IT staff member.
    
  - **Responses to enumerated paths:** Requests to the disallowed paths listed in `robots.txt` returned HTTP 301 redirects. Further activity or access to the underlying resources has not been established from the observations so far.
    
  - **Web server identification:** HTTP responses revealed the web server software and version as `nginx/1.18.0 (Ubuntu)`. This information could help an attacker research vulnerabilities or misconfigurations associated with that software version.
    
</details>

<details>
  <summary><strong>Splunk Investigation</strong></summary>
  
  - **Suspicious User-Agent Activity**. The first query identified repeated activity associated with suspicious User-Agents:

    - **8/21:** Two separate occurrences at 3 AM and 4 AM, each generating 225 events. Source: `odapeeka_access.log`; User-Agent: `feroxbuster`.
    
    - **8/25:** One occurrence at 8 PM, generating 225 events. Source: `odapeeka_access.log`; User-Agent: `feroxbuster`.
    
    - **8/26:** Two separate occurrences at 2 AM and 8 AM, each generating 225 events. Source: `odapeeka_access.log`; User-Agent: `feroxbuster`.
    
    - **8/22:** 77 events associated with the User-Agent `gobuster`, from source `fw:enrich`. This activity was correlated with the client IP `67.205.138.164` identified during the PCAP investigation.
    

  - **Failed HTTP Request Activity**. The second query revealed an average of 7–8 failed HTTP requests per minute. Combined with the identification of Gobuster in the first query, this further supports the assessment that automated web enumeration was taking place.

  - **Discovered Paths**. The third query investigated paths identified during the PCAP investigation to corroborate the findings. Not only were the previously identified paths corroborated, but additional paths were discovered:

    - `/.git`
    
      `/db`
    
    - `/portal`
    
    - `/uploads`
    
    These paths warrant further investigation to determine whether they expose sensitive information or resources that should not be publicly accessible.

  - **Conclusions**

    - Based on the findings, there is evidence of repeated automated web enumeration activity involving the Odapeeka web server. The activity is suspicious, particularly given the repeated use of enumeration tools such as Feroxbuster and Gobuster.

    - However, despite the suspicious nature of the activity associated with IP `67.205.138.164`, we cannot establish malicious intent based on the available evidence alone.

    - Further investigation is warranted to determine whether the discovered paths are intended to be publicly accessible, whether any sensitive resources were successfully accessed, and whether the activity was authorized.
  
</details>

### Module Questions and Answers
<details>
  <summary>Q1. How many requests did the scan make in the PCAP, and how many were not 404?</summary>
  
  - The scan generated 45,896 HTTP requests, of which only 12 received responses other than 404 Not Found.
</details>
<details>
  <summary>Q2. What User-Agent did the scanner send? Would a normal web browser send it?</summary>
  
  - The scanner identified itself as gobuster/3.8.2. A typical web browser would not normally send this User-Agent because it identifies a specialized directory and file enumeration tool.
</details>
<details>
  <summary>Q3. Which directories did robots.txt reveal, and how did the server answer when the scanner asked for each one?</summary>
  
  - The paths listed in robots.txt included / (Allow), /staff, /backup, /config, /logs, and /admin (Disallow). The disallowed paths returned HTTP 301 redirects, while / returned the homepage.
</details>
<details>
  <summary>Q4. Why is Query 2 still useful against a scanner that fakes its User-Agent?</summary>
  
  - Query 2 is still useful because it identifies suspicious behavior based on request patterns, frequency, and volume rather than relying solely on the User-Agent header, which can be spoofed.
</details>
<details>
  <summary>Q5. Name two changes that would have reduced what this scan revealed.</summary>
  
  - Two changes that could have reduced the information exposed or limited the scan's effectiveness are:  
    - Remove sensitive internal information from HTML comments and source code.  
    - Implement detection and response rules to identify and restrict automated web enumeration based on suspicious request patterns.
</details>
