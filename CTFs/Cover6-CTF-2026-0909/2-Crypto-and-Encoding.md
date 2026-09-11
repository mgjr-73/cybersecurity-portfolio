- ## Rotten
    
    `P6F{ebggra_ohg_abg_sbetbggra}`
    
    That's a flag. It's just not in the right order.
    
    - I took the challenge title as a clue... **ROT**ten
    - In [Cyberchef](https://cyberchef.org/), pick one of the rotations until you get something readable  

- ## Base Camp
    
    Somebody encoded this. Then encoded it again. Then, for reasons known only to them, once more. Peel it.
    
    - File base-camp.txt contained `S0Y1RlVWREZHSjRHUVpLWEtaNFdHTUpaTkJSVzJWVEdNSldUU01DWUdKTEhLV0pUSkkyV0dTQ1NPQlJERU5KWg==`
    - A series of alpha-numeric characters and the "==" at the end is tell-tale sign it is likely Base64 encoding.
    - In CyberChef, use `From Base64` in recipe and peel away.
