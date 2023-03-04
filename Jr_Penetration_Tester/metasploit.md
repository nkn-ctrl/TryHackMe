# Metasploit
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/222862293-3c5403ac-f7f7-4385-8a15-4d274a885d41.png" width="300">
</p>

### Main Componets of Metasploit  
`msfconsole`  
- Exploit: A piece of code that uses a vulnerability present on the target system.
- Vulnerability: A design, coding, or logic flaw affecting the target system. The exploitation of a vulnerability can result in disclosing confidential information or allowing the attacker to execute code on the target system.
- Payload: An exploit will take advantage of a vulnerability. However, if we want the exploit to have the result we want (gaining access to the target system, read confidential information, etc.), we need to use a payload. Payloads are the code that will run on the target system.  
<br>

`/metasploit-framework/modules`  
- Auxiliary: Any supporting module, such as scanners, crawlers and fuzzers, can be found here.  
- Encoders: Encoders will allow you to encode the exploit and payload in the hope that a signature-based antivirus solution may miss them.
- Evasion: While encoders will encode the payload, they should not be considered a direct attempt to evade antivirus software.
- Exploits: Exploits, neatly organized by target system.
- NOPs: NOPs (No OPeration) do nothing, literally.
- Payloads: Payloads are codes that will run on the target system.
    - Singles: Self-contained payloads (add user, launch notepad.exe, etc.) that do not need to download an additional component to run.
    - Stagers: Responsible for setting up a connection channel between Metasploit and the target system. Useful when working with staged payloads. “Staged payloads” will first upload a stager on the target system then download the rest of the payload (stage). This provides some advantages as the initial size of the payload will be relatively small compared to the full payload sent at once.
    - Stages: Downloaded by the stager. This will allow you to use larger sized payloads.
- Post: Post modules will be useful on the final stage of the penetration testing process listed above, post-exploitation.  




