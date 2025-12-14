# Host & Network Penetration Testing: System-Host Based Attacks CTF 2

## Lab Environment

In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at http://target1.ine.local and http://target2.ine.local.

Objective: Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

Flags to Capture:

Flag 1:  Check the root ('/') directory for a file that might hold the key to the first flag on target1.ine.local.  
Flag 2: In the server's root directory, there might be something hidden. Explore '/opt/apache/htdocs/' carefully to find the next flag on target1.ine.local.  
Flag 3: Investigate the user's home directory and consider using 'libssh\_auth\_bypass' to uncover the flag on target2.ine.local.  
Flag 4: The most restricted areas often hold the most valuable secrets. Look into the '/root' directory to find the hidden flag on target2.ine.local.

## Tools

The best tools for this lab are:

* Nmap  
* Burp Suite  
* Metasploit Framework


## Solution

- I always begin my process with host discovery before attempting to locate the flags. Afterward, I scan the target to identify which ports are open.

Flag 1:

- An Nmap scan revealed that port 80 was open, hosting an Apache webserver. Further investigation of the web server showed that a CGI script named 'browser.cgi' was active, prompting additional enumeration efforts.  
- I initially employed an Nmap script for the attack: `nmap -sV --script=http-shellshock --script-args "http-shellshock.uri=/browser.cgi" target1.ine.local`.


![][image1]

- To evaluate the Shellshock vulnerability, I chose a manual exploitation method using Burp Suite. The exploit involved executing the command `script () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'`. Following this, I then proceeded to obtain a reverse shell using netcat.   
- I achieved a reverse shell by substituting the user agent's content with the necessary script and establishing a netcat listener on port 1234\.

![][image2]

- I obtained FLAG1\_f4a890b167fd429980ec115a63c2a347 successfully.

Flag 2:

- The second flag was discovered by examining the hidden files within the `/opt/apache/htdocs/` directory. I used the `ls -la` command, which successfully revealed the hidden flag \- FLAG2\_0b3bf8290ddd4ee8bb0cd5e01fb9140e


Flag 3:

- Before finding the flag, I carried out an host discovery scan and also found out that port 22 which is the ssh was opened and it was running the libssh 0.8.3 versioimg3  
     
  ![][image3]  
    
- Successfully obtained a shell session by leveraging the Metasploit framework and the `libssh_auth_bypass` module to bypass the SSH login.


![][image4]

-  Flag 3  was found in the /home/user directory FLAG3\_ce2bcc1350c349c181e57308c9ce78f  
  ![][image5]


Flag 4 

- Flag 4 required privilege escalation to achieve root access. I identified an SUID vulnerability in the `/home/user/welcome` executable. My investigation revealed that the `welcome` file calls a file named `greetings`. I exploited this by deleting the original `greetings` file and replacing it with a new file of the same name, containing the script `cp /bin/bash`.  
  ![][image6]  
    
  ![][image7]  
    
  ![][image8]  
    
- Flag 4 was obtained as FLAG4\_67e8fffec61a481498fe9dbc54a8d79a
