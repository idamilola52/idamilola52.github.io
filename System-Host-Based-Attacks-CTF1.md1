# Host & Network Penetration Testing: System-Host Based Attacks CTF 1

## Lab Environment 

In this lab environment, you will be provided with GUI access to a Kali Linux machine. Two machines are accessible at http://target1.ine.local and http://target2.ine.local.

### Objective: Perform system/host-based attacks on the target and capture all the flags hidden within the environment.

### Useful files:

| /usr/share/metasploit-framework/data/wordlists/common\_users.txt,  /usr/share/metasploit-framework/data/wordlists/unix\_passwords.txt, /usr/share/webshells/asp/webshell.asp |
| :---- |

### Flags to Capture:

**Flag 1**: User 'bob' might not have chosen a strong password. Try common passwords to gain access to the server where the flag is located. (target1.ine.local)  
**Flag 2**: Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)  
**Flag 3:** By attempting to guess SMB user credentials, you may uncover important information that could lead you to the next flag. (target2.ine.local)  
**Flag 4:** The Desktop directory might have what you're looking for. Enumerate its contents. (target2.ine.local)

## Tools

The best tools for this lab are:

- Nmap  
- Hydra  
- Cadaver  
- Metasploit Framework

## Solution

- My typical process starts with host discovery before I even attempt to find the flags. Following that, I scan the target to identify open ports.  
- A scan revealed that the Microsoft IIS server was open.

Flag 1:

- The solution for flag 1 involved using a brute force attack, as the hint suggested that "User 'bob' might not have chosen a strong password. Try common passwords. (target1.ine.local)." Utilizing this information, I employed the Hydra tool and successfully discovered the password for the user 'bob' to be `password_123321`.

![hydra brute force][image1](/system-host/img1.png)  

- Using the credential `bob:password_123321`, I logged into the webserver's WebDAV directory and successfully retrieved the first flag: `fdb88a73fe054fbc98907159a69a60f8`.

![flag 1 ][image2](/system-host/img2.png)  

Flag 2:

- The hint provided for finding flag 2 was: "Valuable files are often on the C: drive. Explore it thoroughly. (target1.ine.local)".  
- My approach was to use the credentials obtained in flag 1 to use the cadaver tool to upload a webshell to the webdav directory on the webserver. 

![cadaver web shell upload][image3](/system-host/img3.png)  

- I then used the backdoor, accessible at `[http://target1.ine.local/webdav/webshell.asp](http://target1.ine.local/webdav/webshell.asp)`, to exploit the WebDAV directory and successfully retrieve `flag2`, which is `e97a78e01a8c455499e1ec478f32549f`.  

![flag 2 ][image4](/system-host/img4.png)  

Flag 3:

- The next flag, flag 3, required attempting to guess SMB user credentials for the target `target2.ine.local`. This was hinted as a potential way to uncover information leading to the flag.  
- I successfully bruteforced the Server Message Block (SMB) credentials using the `auxiliary/scanner/smb/smb_login` module in Metasploit. The administrator password, which was discovered to be "pineapple," to obtain a Meterpreter shell, I used the `exploit/windows/smb/psexec` module.  
    
![smb credentials brute force][image5](/system-host/img5.png)  
    
![flag 3][image6](/system-host/img6.png)  
  Flag3 \- 591eb98c8b72498fb458500b8f977252

Flag 4

- The hint for obtaining flag 4, "(target2.ine.local) The Desktop directory might have what you're looking for. Enumerate its contents," led to its discovery in the Administrator account's Desktop directory. Flag 4 \- f6e7c060b7a14cb7a0466dc9808e48bd

![flag 4][image7](/system-host/img7.png)  

