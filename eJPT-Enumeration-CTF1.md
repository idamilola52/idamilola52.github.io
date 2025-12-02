---
layout: default
---

## Overview 

This lab focuses on enumeration techniques to identify and analyze running services on a target Linux machine. The goal is to explore and interact with the machine's services to uncover and capture hidden flags. Participants will apply their knowledge of network and system enumeration to identify misconfigurations, weak credentials, and potential security vulnerabilities.

## Lab Environment 

A Linux machine is accessible at `target.ine.local`. Identify the services running on the machine and capture the flags. The flag is an md5 hash format.

**Flag 1:** There is a samba share that allows anonymous access. Wonder what's in there\!  
**Flag 2:** One of the samba users has a bad password. Their private share with the same name as their username is at risk\!  
**Flag 3:** Follow the hint given in the previous flag to uncover this one.  
**Flag 4:** This is a warning meant to deter unauthorized users from logging in.

*Note: The wordlists located in the following directory will be useful: /root/Desktop/wordlists*

## Tools

* Nmap  
* Metasploit  
* Hydra  
* enum4linux  
* smbclient  
* Smbmap

## Solution

1. Open the lab link to access the Kali machine.  
     
   ![access the kali machine][image1](/assets/img1.png)  
     
2. Check if the target is up   
     
   ![host discovery][image2](/assets/img2.png)  

3. Upon scanning the target, the following open ports were identified: SMB/Samba, SSH, and FTP.  
     
   ![Nmap -sV target.ine.local][image3](/assets/img3.png)   
   ![Nmap -sV target.ine.local][image4](/assets/img4.png)  
     
4. TThe next step involves performing Server Message Block (SMB) enumeration, utilizing a suitable tool such as `enum4linux`.  
     
- The initial step was to enumerate the users and the operating system. This successfully yielded three users: josh, nancy, and bob.
  
  ![enum4linux -U -O][image5](/assets/img5.png)  

- The next step involved enumerating the available shares, which led to the discovery of the `print$` and `IPC$` shares.

  ![enum4linux -S][image6](/assets/img6)  

5. Since the IPC$ is often referred to as the NULL Session, anonymous login is possible. The `smbclient` tool can be used to bruteforce share names on the target system to discover any that permit anonymous access. The following script was executed to loop through the `share.txt` wordlist for this purpose.  
     
   ![loop to find share name][image7](/assets/img7.png)   
   ![loop to find share name][image8](/assets/img8.png)   
     
   A brute-force attack successfully revealed that the "pubfiles" share is an anonymous working share. Accessing this share directly was the next logical step, which     yielded the flag: `FLAG1{5090e686ad1c4cadb49ed0ada4f94ad5}`.  
     
   ![flag1][image9](/assets/img9.png)    
     
6. The objective is to obtain flag2. We were informed that one of the Samba users has a weak password, and their private share is named identically to their username. The next logical step is to attempt to determine the potential passwords for the users. I plan to use the Metasploit framework to brute-force the passwords for the users: bob, nancy, and josh.  
     
   ![metasploit][image10](/assets/img10.png)    
     
   The auxiliary module `auxiliary/scanner/smb/smb_login` will be utilized.  
     
   ![auxiliary/scanner/smb/smb_login][image11](/assets/img11.png)    
     
   ![smb-login bruteforce][image12](/assets/img12.png)  
     
   * I successfully enumerated the password for the user 'josh' as 'purple'.  
   * However, attempts to obtain passwords for 'bob' and 'nancy' using the same process were unsuccessful.  
   * Next, I used `smbclient` with josh's newly acquired password to connect directly to their private share.  
   * The process resulted in obtaining Flag2: `f844bde5181243df8ff87f96d3405963`.

   ![flag2][image13](/assets/img13.png)  

7. The third flag was obtained using a hint from the second flag's result, which pointed to a running FTP service and suggested checking the banner for additional clues.  
     
-  To begin the enumeration, I performed a full port scan using `nmap`. This scan revealed that an FTP server is operational on port 5554\.

  ![Nmap port scan for FTP][image14](/assets/img14.png)  

- The File Transfer Protocol (FTP) service banner displayed a reminder to users—specifically Ashley, Alice, and Amanda—to immediately change their weak passwords. Since I do not possess their current passwords, I will need to use a tool, such as Hydra, to brute-force the user passwords.

  ![ftp banner][image15](/assets/img15.png)  

- I successfully obtained the password for 'alice', which is "pretty." However, my attempts to find the passwords for 'ashley' and 'amanda' were unsuccessful.

  ![hydra brute force][image16](/assets/img16.png)  

- After logging into the FTP service with the newly acquired credentials, we successfully obtained flag3, which is `FLAG3{0f1972b9556d4aa286383d181347dd1d}`.

  ![flag3][image17](/assets/img17.png)  
  
8. Flag 4 was obtained by exploiting another discovered service on the target machine: SSH. By utilizing the SSH service, I successfully retrieved the flag, which is  `FLAG4{cdee042a27d54e5fb573ff15e2ee6c34}`.  
   
   ![flag4][image18](/assets/img18.png)  
