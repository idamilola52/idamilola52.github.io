---
layout: default
title: eJPT Information Gathering CTF 1
---

## **eJPT Assessment Methodologies: Information Gathering CTF 1 Walkthrough**
**1\. Introduction and Scope**

**1.1 Assessment Overview

This document serves as the formal write-up for the Information Gathering CTF 1 skill check laboratory from the eJPT preparation course. The objective was to methodically apply standard information gathering and reconnaissance techniques against a target web application to identify and extract five hidden flags. This exercise was a crucial step in practicing foundational penetration testing methodologies.

**1.2 Engagement Scope and Objectives

The entire engagement was focused on a single web application hosted at: http://target.ine.local. The primary goal was to capture five distinct flags (FLAG1 through FLAG5) using only non-intrusive and passive reconnaissance methods.

**1.3 Tools and Methodology  
My approach utilized a standard suite of open-source reconnaissance tools, combined with manual analysis:

| Tools  | Purpose |
| :---- | :---- |
| Firefox/Curl | Manual web navigation and checking file headers. |
| Nmap | Service version detection and script scanning. |
| dirsearch/dirb | Automated directory and file brute-forcing. |
| HTTrack | Creating a local, offline mirror of the target website. |

**2\. Detailed Findings and Walkthrough**

This section details the step-by-step process for locating and capturing each flag. I'll frame each flag capture as a finding to maintain a professional report format.

Finding 1: Exposed Sensitive Information via robots.txt

* Description: My initial step in any web application assessment is to check for standard file artifacts. I found that the robots.txt file was openly accessible, and it contained what appeared to be hidden instructions meant for search engine crawlers.

* Steps to Exploit (Proof of Concept):
	 1\. I manually navigated to the standard path: http://target.ine.local/robots.txt  
     2\. The file was immediately accessible and revealed the first flag hidden in a Disallow entry.  
                 
Flag Captured: FLAG1{8ae7dd02add45adaafc4d01c004d473}
       
     
**Finding 2: Unpatched Wordpress CMS Version Detection

* Description: With the first flag captured, my next goal was to identify the underlying technology. Knowing the exact version of a Content Management System (CMS) is critical for exploiting known public vulnerabilities.
  
* Steps to Exploit (Proof of Concept):  
1. Manual Discovery and Verification:  
	* I began by manually inspecting the site content, scrolling to the footer to see which platform designed the site. I quickly noticed the "Designed by WordPress" credit.  
	* To find the specific version, I viewed the page source and inspected the HTML structure. I looked closely at the meta headers within the code, which explicitly stated the version.
	* Result: The manual investigation confirmed the WordPress version is 6.5.3.
  
2. Automated Verification (Nmap):  
	* To confirm this finding and ensure no other services were missed, I ran an automated scan using **Nmap** to identify the running services and their versions: **nmap \-sC \-sV target.ine.local \-p-**  
	* The Nmap output quickly corroborated the manual finding, confirming the WordPress CMS and the version number.
  
3. Flag Retrieval:  
   * With the version confirmed, I performed some final digging by viewing the full page source again and specifically searched for the flag using common keywords like `FLAG` or `FL@G` based on the lab notes.  
   * Running Nmap gave me the flag much easily 
     
 Flag Captured: FL@G2{7aeead80d11b4e08ac7148eca21a1e46}   

 **Finding 3: Exposed Directories via Brute Forcing**

* **Description:** To find any non-standard files or directories, I moved on to automated brute-forcing. I was looking for any directories that might not be linked from the main website structure but are still active on the server.
  
* **Steps to Exploit (Proof of Concept):**  
	1. I used **dirsearch** with its default wordlist against the target:

      dirsearch \-u [http://target.ine.local](http://target.ine.local)

	2. The tool successfully identified several common WordPress directories. The discovery of the /wp-content/uploads directory provided the location-based hint needed to secure the third flag.


Flag Captured: FLAG3{20dafabeb8cf4853970377647e5d0d5b}

**Finding 4: Overlooked Backup Configuration File**

* Description: After running the directory brute-force, I carefully reviewed the complete list of files and directories found. I noticed an unusual file with a .bak extension, which is a classic indicator of a leftover, sensitive backup.
   
* Steps to Exploit (Proof of Concept):  
	1. The output from dirsearch indicated the presence of the file */wp-config.bak*.  
	2. I used Curl to quickly fetch the content of this file directly:

			 curl http://target.ine.local/wp-config.bak
    
	4. The file contents were dumped, revealing the sensitive configuration details and the fourth flag.

Flag Captured: FLAG4{c026ef0bd6274ec493c821f2447e5419}

#### **Finding 5: Sensitive Information Exposed in XML-RPC Configuration Backup**

* **Description:** For the final flag, I shifted my methodology to look for files that might be referenced internally but not directly accessible or linked to. Creating a local mirror allows for a deep, static analysis of all website components.  
* **Steps to Exploit (Proof of Concept):**  
	1\. I used **HTTrack** to download a complete, local copy of the target website:
  
	               *httrack http://target.ine.local
  
	2\.   After the download completed, I began manually inspecting the downloaded files and noticed a file named xmlrpc0db0.php.   
	3\.    I viewed the content of this file locally using a text editor (`nano`):
  
	                nano xmlrpc0db0.php
  
	4\. The file contained the final flag, which was hidden within the XML-RPC configuration data.

Flag Captured: FLAG5{2857ef1a83054ef397fb885e9aed3a1b}

### **3\. Conclusion**

The Information Gathering CTF 1 was successfully completed by applying a comprehensive and repeatable methodology. By systematically moving from passive checks (`robots.txt`), to version identification (`Nmap`), to automated content discovery (`dirsearch`), and finally to deep static analysis (`HTTrack`), all five flags were successfully captured. This exercise reinforced the critical role of thorough reconnaissance as the foundation of any successful penetration test.
