- ## Shorthand 
    IPv6 addresses can be written more than one way, and people get lazy.
    
    Expand this to its full, uncompressed form. That's your flag, lowercase, colons replaced with underscores.
    
    `2001:db8::c6:0:0:6`
    
    - IPv6 address must contain 8 hextets (`1st hextet:2nd hextet: --> :7th hextet:8th hextet`)
    - Each hextet = 4 hexadecimal digits
    - `::` fills in however many `0000` hextets are needed to reach 8, but you can only use double colon once for one or more consecutive 0000 hextets. Other individual 0000 hextets must be represented with :0:
    - Example, take `2001:0db8:0000:0000:1234:0000:0000:0001`
        - can be reduced to `2001:db8:0:0:1234:0:0:1`
        - can be further reduced to `2001:db8::1234:0:0:1` or `2001:db8:0:0:1234::1` but NOT `2001:db8::1234::1`
        - if there are multiple runs of zeros of equal length (like the example), RFC 5952 recommends compressing the **leftmost** run, therefore `2001:db8::1234:0:0:1`
    - The challenge wants us to do the reverse  
        `2001:0db8:0000:0000:00c6:0000:0000:0006`  
        
- ## Nobody Turned It Off
    Odapeeka State runs an IPv4-only network. That's what the policy says.
    
    Here's a capture from their office segment. The policy is wrong - prove it, and tell us the address that shouldn't be there. Submit it exactly as it appears in the capture, colons replaced with underscores.
    
    - Two things popped out of the pcap  
        <img width="1575" height="158" alt="image" src="https://github.com/user-attachments/assets/d4b12dc8-bf11-4d7b-a9cb-8a4f44a8432f" />
        - If IPv4-only network policy is strictly enforced, `2001:db8:6:c6:1:1:1:9` shouldn't be able to participate in Neighbor Solicitation.
        - The same host `2001:db8:6:c6:1:1:1:9` subsequently established a TCP connection and HTTP communications over IPv6.   
            
- ## Neighborly
    On an IPv6 network, machines introduce themselves. Anyone can make an introduction - including someone claiming to be the router.
    
    Who's lying in this capture? Not every Router Advertisement here comes from Odapeeka State's real gateway - and the impostor isn't exactly being subtle about it once you know what a router actually behaves like.
    
    Flag format: `C6S{mac_address_with_underscores}` - the rogue router's source MAC.
    
    - The obvious rouge router is `fe80::de:adff:febe:ef99 with` MAC `02:de:ad:be:ef:99`  
        <img width="1269" height="341" alt="image" src="https://github.com/user-attachments/assets/9e4b3895-2db4-4b9a-a040-1dd5cea12097" />
    - A legitimate IPv6 router doesn't normally need to advertise itself every couple of seconds continuously (in the timestamp, every 2 seconds). The MAC address is advertised as shown in the Info column.  
  
- ## The Long Way Around
    Scanning an IPv4 /24 takes seconds. Scanning an IPv6 /64 - 18 quintillion addresses - would take longer than the universe has existed at any realistic packet rate.
    
    But every IPv6 host on a segment is required to listen on one specific, well-known multicast address whether it wants to or not - which is exactly how hosts actually get discovered on IPv6 networks, without ever brute-forcing the address space.
    
    Name that address, written exactly the way it's normally written.
    
    Flag format: colons -> underscores, so `ff02::1` becomes `C6S{ff02__1}` (double underscore for the double colon).
    
    - `ff02::1` *IS* the IPv6 All-Nodes multicast address  
    
