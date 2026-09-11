- ## Beacon Watch
    
    A workstation at Odapeeka State has been quietly checking in with a host outside the network, over and over, like clockwork. Nothing about any single request looks wrong.
    
    Download the capture. Find the compromised account and how often it phones home.
    
    Flag format: `C6S{account_beacons_every_Ns}`.
    
    - I could see in the packets HTTP communications, specifically the ones with "GET /c2/beacon.php" so I used filter `frame contains "beacons"` for a cleaner output.  
        <img width="1414" height="443" alt="image" src="https://github.com/user-attachments/assets/53afbd59-3a3e-4c10-852d-c23255c1485a" />
    - Just from visual inspection, I could tell the beacon is every 30s.
    - Getting the flag format correctly took me for a loop. I just substituted N with 30 but I kept getting the wrong answer... after so many tries, I thought maybe substitute "account" with the actual account name (the value after `?id=` in the URL)??? It worked!  
        
- ## What Did They Take
    
    Same session, later. A file left the network.
    
    Recover it. The flag is inside.
    
    - For this one I just followed the TCP stream and found the flag in stream 1... but to answer the question, "What Did They Take?", it's the `records_memo_draft.txt`  
        <img width="544" height="422" alt="image" src="https://github.com/user-attachments/assets/69e430e7-031f-4a74-86e8-44d31f8ce96d" />
        
- ## Ring Ring
    
    DNS is the most honest protocol on any network. It tells you where everything wanted to go, even when it didn't get there.
    
    Here's a capture from the Odapeeka State office segment. Something in it asked a question it had no business asking.
    
    Flag format: `C6S{queried_domain_with_underscores}`.
    
    - In WireShark i used the DNS query filter `dns.flags.response == 0`  
        <img width="1398" height="459" alt="image" src="https://github.com/user-attachments/assets/86105f1c-f465-414b-b61e-3e203702ee1c" />
        
    - I could see several repeated domains so I started thinking "What's normal and abnormal?" I needed to isolate unique domains. I used tshark so I could extract text better.
        
    - I was on a Windows desktop so I had to use Powershell. I'm familiar with PS but it is not my strong suit. It was the best time to learn something new. I asked ChatGPT for a Powershell command that would let me parse DNS packets and extract just the unique domains and give me a count of each occurrence.
        
        ```ps
         "C:\Program Files\Wireshark\tshark.exe" `
          -r "C:\path\to\capture.pcap" `
          -Y "dns.flags.response == 0" `
          -T fields -e dns.qry.name |
          Sort-Object |
          Group-Object |
          Sort-Object Count -Descending
        ```
        
    - I tried it line by line to see what kind of output I would get for each one... I just navigated to where tshark.exe was located to make things simpler.  
        `PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap"`  
        <img width="962" height="396" alt="image" src="https://github.com/user-attachments/assets/cea41f70-069a-4b1e-9b23-1e452d2a9521" />
        
        `PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap" -Y "dns.flags.response == 0"`  
        <img width="938" height="424" alt="image" src="https://github.com/user-attachments/assets/4311cf18-c605-4971-8529-7b1682c14a26" />

        `PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap" -Y "dns.flags.response == 0" -T fields -e dns.qry.name`  
        <img width="963" height="435" alt="image" src="https://github.com/user-attachments/assets/c26f2c26-8c30-40e3-a3d9-fdd666bc5d72" />

        `PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap" -Y "dns.flags.response == 0" -T fields -e dns.qry.name | Sort-Object`  
        <img width="965" height="598" alt="image" src="https://github.com/user-attachments/assets/1cb96a6e-ef4a-44b7-92d9-3b44efa3cfb3" />

        `PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap" -Y "dns.flags.response == 0" -T fields -e dns.qry.name | Sort-Object | Group-Object`  
        <img width="968" height="271" alt="image" src="https://github.com/user-attachments/assets/beac51ce-ac18-4535-a7e3-8522aaa4b01e" />
 
        <br/>`PS C:\Program Files\Wireshark> ./tshark -r "C:\Users\mgjr7\Downloads\20-ring-ring.pcap" -Y "dns.flags.response == 0" -T fields -e dns.qry.name | Sort-Object | Group-Object | Sort-Object Count -Descending`  
        <img width="965" height="273" alt="image" src="https://github.com/user-attachments/assets/929bf8a8-d840-4655-bf5f-4bcedf22ba21" />

    - Given the results, I could see the frequency of each queried domain and infer that domains queried multiple times were likely the ones used for normal business, and I could also tell most of the domains themselves were legitimate:
        
        | Queries | Domain | Interpretation |
        | ---: | --- | --- |
        | 19  | `windowsupdate.microsoft.com` | Expected Windows activity |
        | 13  | `outlook.office365.com` | Expected Microsoft 365 activity |
        | 13  | `s3.amazonaws.com` | Common cloud service |
        | 11  | `time.windows.com` | Normal time synchronization |
        | 11  | `api.slack.com` | Collaboration service |
        | 11  | `cdn.jsdelivr.net` | Common CDN |
        | 9   | `fonts.googleapis.com` | Common web resource |
        | 8   | `www.google.com` | Normal/general Internet activity |
        | 8   | `safebrowsing.googleapis.com` | Common Google security service |
        | 7   | `github.com` | Common development service |
        | 1   | `sync-relay-cdn.net` | ??? |
        
        
        Except for `sync-relay-cdn.net` This is the outlier and had no obvious relationship or other activity compared to the other domains on the list. However, I could not tell if it was malicious. It was just an interesting artifact.
        
        For the flag, I think I tried a combination of just substituting the dot with underscore, then the hyphens as well.  
        
- ## Handshake Problems
    
    The connection is encrypted. You can't read the traffic.
    
    You can read who they said they were.
    
    Every TLS handshake exchanges a certificate in the clear, before anything is encrypted. Look at what this one actually presented versus what the client was trying to reach.
    
    Flag format: `C6S{certificate_cn_with_underscores}`.
    
    - Had to Google/ChatGPT this one as I was not familiar with TLS Handshake details.
    - Looked at packet labeled Certificate and drilled down Transport Layer Security > Certificates > Certificate > signedCertificate > subject. The answer is in the rdnSequence value.  
        <img width="665" height="420" alt="image" src="https://github.com/user-attachments/assets/592ce8b0-4f57-409d-b93a-bb69f4f72e06" />

    - Again, I played with the flag format here.  
        
- ## Slow Leak
    
    No file left this network. No connection looks unusual. Nothing tripped.
    
    Data still got out. Reassemble it.
    
    DNS doesn't only carry answers back - sometimes the question itself is the payload. One domain suffix keeps repeating in this capture. Pull out every query against it, in the order they appear, and look at what's actually being asked.
    
    Flag format: `C6S{lowercase_with_underscores}` - submit exactly what you decode.
    
    - Used filter `dns.flags.response == 0 && frame contains "odapeeka"` for a cleaner output  
        <img width="1329" height="435" alt="image" src="https://github.com/user-attachments/assets/b0568fa2-20ef-4742-a3bc-c96cc54c8ef3" />

    - Notice the prefixes for odapeekastate-sync.net. At first glance, they looked like hex.
    - I assembled them in Notepad. `4336537b646e735f69735f6f745f615f7369...` and so on...
    - Plugged the whole sequence in CyberChef and used the appropriate recipe and got the flag.  
