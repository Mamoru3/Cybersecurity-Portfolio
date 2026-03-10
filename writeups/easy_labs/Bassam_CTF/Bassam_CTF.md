# Cybersecurity Portfolio
## Bassam CTF writeup - Local Network

After downloading and opening the box from vulnhub : https://www.vulnhub.com/entry/bassamctf-1,631/, we launch it in VMware and log into our main machine.

First things first, we open our cmd and start with a ping sweep to get an idea of who is alive on our network without causing too much network noise.
```
nmap -sn 192.168.1.0/24
```  
![Nmap scan](Evidence/ping_sweep.png)
