# Assessment Methodologies: Footprinting and Scanning CTF 1

## 📜 Task Overview

Reconnaissance is the initial phase of a penetration testing process where information about a target system is gathered to identify potential vulnerabilities. This phase involves actively or passively collecting data such as server headers, open ports, exposed directories, and system configurations. Techniques like scanning, querying DNS records, examining web application files (e.g., `robots.txt`), and analyzing response headers help uncover critical information that can aid in later exploitation phases. Effective reconnaissance allows testers to map the attack surface, prioritize targets, and plan their approach with minimal detection by the system's defenses.

## 🎯 Lab Objective

This lab is designed to test knowledge and skills in performing reconnaissance and identifying hidden information on a target web server.

-   **Environment:** GUI access to a Kali Linux machine.
-   **Target:** `http://target.ine.local`
-   **Goal:** Perform reconnaissance and capture all four flags hidden within the environment.

### Flags to Capture

1.  **Flag 1:** The server proudly announces its identity in every response. Look closely; you might find something unusual.
2.  **Flag 2:** The gatekeeper's instructions often reveal what should remain unseen. Don't forget to read between the lines.
3.  **Flag 3:** Anonymous access sometimes leads to forgotten treasures. Connect and explore the directory; you might stumble upon something valuable.
4.  **Flag 4:** A well-named database can be quite revealing. Peek at the configurations to discover the hidden treasure.

### Tools Used

* Nmap
* FTP
* MySQL

> **Note:** The flag format is `FLAGx_MD5Hash`. Only the MD5 hash string (excluding the underscore) needs to be submitted. E.g., `0f4d0db3668dd58cabb9eb409657eaa8`.

---

## 💻 Solution Write-up

### Initial Reconnaissance

Before starting the active scan, I ensured the target was up and accessible using a simple host discovery method.

Bash
ping target.ine.local

Port Scanning and Service Enumeration
The next step was to run a comprehensive port scan to identify open ports and services running on the target machine.

Bash

nmap target.ine.local
A more detailed scan was then performed to identify service versions, operating system details, and to run default enumeration scripts.

Bash

nmap target.ine.local -p- -A

Flag 1: Server Header Enumeration
By analyzing the detailed Nmap scan result, the HTTP server response header was found to contain the first flag.

Flag 1: FLAG1_511010b9a0404e478c4b6ea7176620e6

Flag 2: The Gatekeeper's Instructions (robots.txt)
Following the hint to check the gatekeeper's instructions, I navigated to the robots.txt file on the web server. This file contained a disallowed entry that pointed to a hidden file named secret-info, which contained the second flag.

Flag 2: FLAG2_9924bc87e8b74e2eaea8f45163515df5

Flag 3: Anonymous Access (FTP)
The initial Nmap scan indicated that the FTP port was open. Based on standard service misconfigurations, I attempted an anonymous login.

Credentials Used:

Username: Anonymous

Password: (Left blank)

The anonymous login was successful and revealed two files: creds.txt and flag.txt. I retrieved both files, and flag.txt contained the third flag. The creds.txt file contained credentials for the MySQL database, which would be useful for the final flag.

Flag 3: FLAG3_45b8c6b93052444eabd9da1ec8864744

Flag 4: Database Configuration (MySQL)
With the database credentials obtained from the FTP server's creds.txt file, I was able to log into the MySQL server.

Command to log in:

Bash

mysql -u db_admin -h target.ine.local -p
# [password obtained from creds.txt]
Once logged in, I enumerated the databases and tables to find the final flag.

Flag 4: FLAG4_76d054d1c8d740d3b7087c85d334810e
