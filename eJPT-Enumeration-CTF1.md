---
layout: post
title: "Assessment Methodologies: Enumeration CTF1 Write-up"
date: 2025-11-22 14:00:00 -0500 # Adjust the date/time as needed for publishing
categories: [ctf, enumeration, writeup]
tags: [samba, smb, ftp, ssh, nmap, metasploit, hydra]
description: "A step-by-step write-up for the CTF1 challenge focusing on network and service enumeration techniques."
---

# Assessment Methodologies: Enumeration CTF1

## 💡 Overview

This lab focused on enumeration techniques to identify and analyze running services on a target Linux machine. The goal was to explore and interact with the machine's services to uncover and capture hidden flags. Participants applied knowledge of network and system enumeration to identify misconfigurations, weak credentials, and potential security vulnerabilities.

---

## 💻 Lab Environment

A Linux machine is accessible at `target.ine.local`. The objective was to identify the services running on the machine and capture the flags, which are in **MD5 hash format**.

* **Flag 1:** There is a Samba share that allows anonymous access.
* **Flag 2:** One of the Samba users has a bad password. Their private share with the same name as their username is at risk!
* **Flag 3:** Follow the hint given in the previous flag to uncover this one.
* **Flag 4:** This is a warning meant to deter unauthorized users from logging in.

> **Note:** The wordlists located in the following directory will be useful: `/root/Desktop/wordlists`

### Tools Used

* **Nmap**
* **Metasploit**
* **Hydra**
* **enum4linux**
* **smbclient**
* **Smbmap**

---

## ✅ Solution

### 1. Initial Access and Service Discovery

1.  Open the lab link to access the Kali machine.
    

2.  Check if the target is up.
    `ping -c 4 target.ine.local`

3.  Upon scanning the target using **Nmap**, the following open ports and services were identified: *SMB/Samba**, **SSH**, and **FTP**.


### 2. Capturing Flag 1: Anonymous SMB Share

The next step involved performing Server Message Block (SMB) enumeration, utilizing **enum4linux**.

* The initial step was to enumerate the users and the operating system. This successfully yielded three users: **josh**, **nancy**, and **bob**.
    

* The next step involved enumerating the available shares, which led to the discovery of the `print$` and `IPC$` shares.
    

Since the `IPC$` share often permits the **NULL Session**, anonymous login is possible. The `smbclient` tool was used to brute-force share names on the target system to discover any that permit anonymous access.

* A script was executed to loop through a `share.txt` wordlist to identify anonymous shares.
    
    

* The brute-force attack successfully revealed that the **"pubfiles"** share is an anonymous working share. Accessing this share directly was the next logical step, which yielded **Flag 1**:
    `FLAG1{5090e686ad1c4cadb49ed0ada4f94ad5}`.
    

### 3. Capturing Flag 2: Weak SMB User Password

The objective for Flag 2 was to find a Samba user with a weak password, as their private share is named identically to their username. The next step was to brute-force the user passwords.

* The **Metasploit framework** and the auxiliary module **`auxiliary/scanner/smb/smb_login`** were utilized against the enumerated users: **bob**, **nancy**, and **josh**.
    
    

* The process successfully enumerated the password for the user **'josh'** as **'purple'**. Attempts to obtain passwords for 'bob' and 'nancy' were unsuccessful.

* The user `smbclient` was then used with josh's newly acquired credentials (`josh:purple`) to connect directly to their private share (`//target.ine.local/josh`).

* Accessing the private share resulted in obtaining **Flag 2**:
    `FLAG2{f844bde5181243df8ff87f96d3405963}`.
    

### 4. Capturing Flag 3: FTP Service Vulnerability

Flag 3 was obtained using a hint from the second flag's result, which pointed to a running **FTP service** and suggested checking the banner for additional clues.

* A full port scan using **Nmap** revealed that an FTP server is operational on port **5554**.
    

* Connecting to the File Transfer Protocol (FTP) service revealed a banner displaying a reminder to users—specifically **Ashley**, **Alice**, and **Amanda**—to immediately change their weak passwords. Since the current passwords were unknown, **Hydra** was used to brute-force the user passwords.
    

* **Hydra** was successfully used to obtain the password for **'alice'**, which is **"pretty"**. Attempts to find the passwords for 'ashley' and 'amanda' were unsuccessful.
    

* After logging into the FTP service with the newly acquired credentials (`alice:pretty`), **Flag 3** was successfully retrieved:
    `FLAG3{0f1972b9556d4aa286383d181347dd1d}`.
    

### 5. Capturing Flag 4: SSH Service Warning

Flag 4 was obtained by exploiting another discovered service on the target machine: **SSH**.

* By simply connecting to the **SSH service** on the target machine using the `ssh` command, a pre-login warning banner (often called the **Message of the Day** or **MOTD**) was displayed before any credentials were entered. This warning banner contained the final flag.

* Successfully retrieved **Flag 4**:
    `FLAG4{cdee042a27d54e5fb573ff15e2ee6c34}`.
