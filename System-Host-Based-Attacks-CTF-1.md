# Host & Network Penetration Testing: System-Host Based Attacks CTF 1

## Lab Environment

In this lab environment, you will have GUI access to a Kali machine. The target machine will be accessible at target.ine.local.

## Objective

Use Metasploit and manual investigation techniques to capture the following flags:

## Flags to Capture:

Flag 1: Gain access to the MSSQLSERVER account on the target machine to retrieve the first flag.  
Flag 2: Locate the second flag within the Windows configuration folder.  
Flag 3: The third flag is also hidden within the system directory. Find it to uncover a hint for accessing the final flag.  
Flag 4: Investigate the Administrator directory to find the fourth flag.

## Tools

The best tools for this lab are:

* Nmap  
* Metasploit Framework  
* mssql

## Solution 

- I will begin this lab by confirming connectivity to the target machines using a simple ping. After host discovery, I will utilize Nmap for service version detection. Although numerous ports are open, port 3389 indicates a Microsoft SQL Server service is running on that port.

  ![host discovery and nmap scan][image1](/system1/img1.png)


- A more in-depth enumeration of the `mssqls` service, which is operational on port 3389, yielded noteworthy details concerning the SQL server.

  
![further enumeration of the mssql service][image2](/system1/img2.png)

Flag 1:

- The MS-SQL Server, based on the gathered information, is susceptible to the `windows/mssql/mssql_clr_payload` Metasploit exploit, which successfully provides a Meterpreter session on the target system.  
  ![exploit and meteroreter session][image3](/system1/img3.png) 
    
- Leveraging the `SeImpersonatePrivilege` set on the system, I successfully escalated permissions from `NT Service\MSSQLSERVER` to the highest privilege level, `NT AUTHORITY\SYSTEM`, using the `getsystem` command within Meterpreter.  
    
- Subsequently, I executed the shell command to gain access to the Windows shell. This allowed me to retrieve Flag 1, which was located in the `C:\` directory and had the value `9937f7c9f8fb44e8900f54f6c52b25b7`.  
  ![flag1][image4](/system1/img4.png)


Flag 2:

- Following the hint that the second flag was located in the Windows configuration folder, I accessed `C:\Windows\System32\config`. This allowed me to retrieve flag 2, which is `458165fd91aa429ba5d3a977edcf0ea9`.  
  ![flag2][image5](/system1/img5.png)  


Flag 3: 

- Flag 3 was located within the system directory, as indicated by the hint. I used the command `dir \*.txt /s /b` within the `C:\\Windows\\System32` directory to search for and successfully find the hidden text file containing the flag: `d339184ab36a4234bce3b02776f4ad5a`.  
  ![flag3][image6](/system1/img6.png)

Flag 4:

- To locate the fourth flag, we must escalate our privileges to gain access to the Administrator directory, as suggested by the hint for flag 3\.  
- I first migrated to the `lsass.exe` process, which holds the `NT AUTHORITY \\SYSTEM` credentials. This allowed me to access the flag, `70eb75a78b8a479c95958826f8354f8b`, located on the desktop at `C:\\Users\\Administrators\\Desktop`.  
    
  ![flag4][image7](/system1/img8.png)  
    
