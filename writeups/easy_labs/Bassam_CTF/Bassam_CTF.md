# Cybersecurity Portfolio
## Bassam CTF writeup - Local Network

After downloading and opening the box from vulnhub : https://www.vulnhub.com/entry/bassamctf-1,631/, we launch it in VMware and log into our main machine.

First things first, we open our cmd and start with a ping sweep to get an idea of who is alive on our network without causing too much network noise.
```
nmap -sn 192.168.1.0/24
```  
![Nmap scan](Evidence/ping_sweep.png)
  
As the target machine is on the local network, it is easy to identify, thanks to also the host name being "VMWare"  
Here we acquire the important information that our target is located at the IP: 192.168.1.55.  
  
![Target found ping sweep](Evidence/target_found.png)  

Finally we can run a more targeted scan on the target IP.
  
```
nmap -sS -sV -p- -T4 --open 192.168.1.55
```
![Nmap accurate scan](Evidence/accurate_scan.png)  
This nmap scan shows which ports are open and which services/versions are running on these ports.
  
![Result accurate scan](Evidence/accurate_scan_result.png) 

The ports that are open are only two, and the services running on the target machine are:
 - 22 SSH OpenSSH 7.6p1 Ubuntu
 - 80 http Apache httpd 2.4.29  
Immediately from the result of the scan we can have an idea of the enumeration and possible exploitation approach.
There will be some sort of web application, through which we will hopefully be able to gain credentials to either bruteforce or normally login into SSH.

Before opening the browser and going into the target's website, I decide to curl the address to see headers and hidden fields the browser would not normally show.  

```
curl -I 192.168.1.55:80
```
In this case the curl does not show anything apart form "Bassam.ctf", which is probably the target's virtual host.
  
![Target curl result](Evidence/curl_-i.png) 

I go then into my /etc/hosts and add bassam.ctf as a host for the 192.168.1.55 address.
  
![host added](Evidence/virtual_host_added.png) 

Now by curling bassam.ctf, we get the message "Welcome to my blog"

![host added](Evidence/welcome_to_my_blog.png)  

By opening the browser and searching 
```
http://192.168.1.55:80
```
We get to an empty web page,
  
![Web Page](Evidence/blank_page.png)  

Intuitively I now decide to explore the bassam.ctf website in the browser: 
```
http://bassam.ctf/
```
We get to a white page that welcomes us to the main blog "Welcome to my blog".
  
![Welcome to my blog page](Evidence/blog_page.png)
  
Here I decide to choose the path to try to find other possible subdirectories and go deeper in the enumeration.  
I open gobuster and run a search on the possible existing subdirectories.
```
gobuster dir -u http://bassam.ctf -x php, lua, hmtl, txt -w "wordlist.txt"
```  

Whilst I was hopeful to find existing subdirectories that would give me a clear path, no subdirectories were found other than "index.html".
  
![gobuster search ](Evidence/result_gobuster_scan.png)  
  
Feeling a bit lost with no clear path to follow, I decide to take make two choices:
- Run a vulnerability scan with nmap.
- Run a subdomain scan with ffuf on the bassam.ctf domain.

Starting with the vulnerability scan:
```
nmap 192.168.1.55 -sV -sS -p22,80 -T4 --script vuln
```
A huge list of possible exploits comes out, requiring some time to research them, of course in order of how severe they are.
  
![nmap vuln scan ](Evidence/ssh_nmap_vulns.png)  

In this very moment I thought that there would be more to a blog than just a welcoming page.

Here I run the Vhost discovery with ffuf.
  
![ffuf subdomain scan](Evidence/ffuf_discovery.png)
  
And very positively I get a result that there is a subdomain called "Welcome".
  
![ffuf subdomain found](Evidence/ffuf_welcome_result.png)

I immediately add it in the /etc/hosts file.
  
![ffuf subdomain found](Evidence/welcome_bassam_hosts.png)

And open the new web page at:
```
http://welcome.bassam.ctf
```
On the web browser, being welcomed, once again, to an empty page.  
I immediately decide to inspect the page and see a comment that says "Open your eyes".
  
![Open your Eyes](Evidence/open_your_eyes.png)
  
Another gobuster search for subdirectories, same as before (but of course changing the target to be "welcome.bassam.ctf), reveals two php files available, good enumeration always comes in handy.
  
![gobuster last scan](Evidence/gobuster_final_scan_result.png)
  
These websites are not what I expected them to be.
Config.php was empty, but index.php revealed to be an interesting page.
Upon opening "http://welcome.bassam.ctf/index.php we are welcomed by a white page with an input box and a "download" button. After trying to run commands and download files (which did not work) I decide to type "config.php" and press enter, and this surprisingly downloaded the config.php which we could not inspect before in the browser.
  
![gobuster last scan](Evidence/config_php_in_field.png)

Upon opening, the file revealed a username and password, giving me a path to try in port 22 for the SSH service to establish a connection with the machine.
  
![gobuster last scan](Evidence/credentials.png)

Now by 
```
ssh test@192.168.1.55
```
We are able to login into ssh by inputting the given password (test123).  
I kind of regretted not bruteforcing here.  

The connection is now successful, and we are in the machine as user Test.
  
![sshd](Evidence/ssh_login.png)

The privilege escalation part to root starts.  
Here I immediately start with some enumeration to understand which permissions I have and what the OS/Kernel versions this target machine has.
  
![sshd](Evidence/basic_linux_enum.png)  

Some research on these versions does not really show immediate exploits, so I decide to move onto the system exploration, and list all the files and hidden files in the test directory by:
```
ls -la
```
  
![lsla](Evidence/ls_la.png)

I also run 
```
sudo -l
```
To see my current privileges.
  
After some bottomless system exploration, I see a "PassProgram" folder in the root folder.

![passprogramfolderfound](pass_program_find.png)




