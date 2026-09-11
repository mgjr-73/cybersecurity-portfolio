- ## View Source
    
    The Odapeeka State Department of Records just launched a new site. They were in a hurry.
    
    Target: `http://target.cover6solutions.com`
    
    Before you touch a tool, look at what the page is already telling you.
    
    - Go to http://target.cover6solutions.com
    - Open browser developer tool (I used Firefox. Your developer tool might differ slightly if using a different browser.)
    - In inspector, expand all html elements. Flag is in a comment within `<head>` element.
        
- ## Ask Politely  
    Most sites keep a file telling search engines where *not* to look. It's a suggestion, not a lock - and it's a map of everything they'd rather you didn't find.
    
    - Just append robots.txt to the url  
         
- ## Read the Envelope  
    A page is more than what you can see. Every response carries headers - the envelope it arrived in. Somebody at Odapeeka State left something in theirs.
    
    - Back on the developer tool go to Network > Headers.
    - Under response headers the flag is in x-secret-token field.  
        
- ## Nobody Cleans Up   
    Robots.txt told you where they didn't want you looking. Go look.
  
    - The flag is in /admin-backup/readme.txt
      
- ## The Front Desk  
    There's a staff portal. It's password protected, which would matter more if anyone had changed the password.
    
    - Robots.txt also included /staff/. If you try to access it, you will be prompted for username and password.
    - The password clue is in the readme.txt. As for username, first instinct is to try "admin" since it is usually the default.
    - The flag is immediately revealed once you gain access.  
       
- ## Cookie Jar
    You're logged into the staff portal as a clerk. The site decides what you're allowed to see by asking your browser who you are. That's the mistake. Exploit it.
    
    - Notice in the previous `readme.txt`, the password was revealed.
    - We also got a clue about old cookie-based session logic.
    - At the login portal, I tried admin:<password-from-readme.txt>
    - Once in, there is a link to admin access but you are initially logged in as "clerk" so it is restricted
    - Inspecting the html, we see comment about session handling is on client side only. It is using client-side `role` cookie. As we can see, "clerk" is encoded in base64.  
        <img width="1146" height="132" alt="image" src="https://github.com/user-attachments/assets/d1fae2d8-41cf-4125-b494-c59a1a1461ec" />
    - Go to Storage > Cookies and change the base64 for "clerk" to base64 of "admin". I used [Cyberchef](https://cyberchef.org/) to convert it.
        <img width="857" height="167" alt="image" src="https://github.com/user-attachments/assets/90b4ce2b-a3e8-4fa2-bcca-0e68e94c6169" />
    - Click on the admin access link and the flag will be revealed  
        
- ## Parameter Tampering  
    The records portal shows you your own file. It decides which file that is by asking you. Target: `http://target.cover6solutions.com/records/` IDOR. Iterate `?id=` to find the flagged record.
    
    - IDOR (Insecure Direct Object Reference) is an access control vulnerability where an application exposes a direct reference to an object, allowing an attacker to manipulate that reference to access unauthorized data or resources.
    - As the instructions said, iterate the id and you will eventually get to the flag.  
       
- ## Handle Hunt  
    Odapeeka State publishes a staff directory. Four employees, four profile pages, four sets of details that mostly agree. Target: http://target.cover6solutions.com/directory/ One of them is lying about something. Find the inconsistency.
    
    - Just check each staff member. One of them will have the flag.  
        
