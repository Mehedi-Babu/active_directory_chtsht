# Active Directory Exploitation Cheatsheet

A cheatsheet that contains common enumeration and attack methods for Windows Active Directory.

This cheatsheet is inspired by the [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) repo.

**Credit Info:** Every tool and methodology i am describing will be referenced at the end, with a link to the actual tool and creator.

![Just Walking The Dog](https://github.com/buftas/Active-Directory-Exploitation-Cheatsheet/blob/master/WalkTheDog.png)

## Summary

- [Active Directory Exploitation Cheatsheet](#active-directory-exploitation-cheatsheet)
  - [Summary](#summary)
  - [Tools](#tools)
  - [Enumeration](#enumeration)
  - [Local Privilege Escalation](#local-privilege-escalation)
  - [Lateral Movement](#lateral-movement)
  - [Domain Persistence](#domain-persistence)
  - [Domain Privilege Escalation](#domain-privilege-escalation)
  - [Cross Forest Attacks](#cross-forest-attacks)
  - [Forest Persistence](#forest-persistence)
  - [References](#references)

## Tools
- [PowerSploit](https://github.com/PowerShellMafia/PowerSploit/tree/dev)
- [PowerUpSQL](https://github.com/NetSPI/PowerUpSQL)
- [Powermad](https://github.com/Kevin-Robertson/Powermad)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [Mimikatz](https://github.com/gentilkiwi/mimikatz)
- [Rubeus](https://github.com/GhostPack/Rubeus) -> [Compiled Version](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries)
- [BloodHound](https://github.com/BloodHoundAD/BloodHound)
- [AD Module](https://github.com/samratashok/ADModule)

## Enumeration

[PowerView 3.0 Tricks](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

### With PowerView

- **Get Current Domain:** `Get-NetDomain`
- **Enum Other Domains:** `Get-NetDomain -Domain <DomainName>`
- **Get Domain SID:** `Get-DomainSID`
- **Get Domain Policy:** 
```
Get-DomainPolicy
#Will show us the policy configurations of the Domain about system access or kerberos
(Get-DomainPolicy)."system access"
(Get-DomainPolicy)."kerberos policy"
```
- **Get Domain Controlers:** 
```
Get-NetDomainController
Get-NetDomainController -Domain <DomainName>
```
- **Enumerate Domain Users:** 
```
Get-NetUser
Get-NetUser -Username <user> 
Get-NetUser | select cn
Get-UserProperty
#Check last password change
Get-UserProperty -Properties pwdlastset

#Get a spesific "string" on a user's attribute
Find-UserField -SearchField Description -SearchTerm "wtver"
```
- **Enum Domain Computers:** 
```
Get-NetComputer -FullData
Get-DomainGroup

#Enumerate Live machines 
Get-NetComputer -Ping
```
- **Enum Group Policies:** 
```
Get-NetGPO

# Shows active Policy on specified machine
Get-NetGPO -ComputerName <Name of the PC>
Get-NetGPOGroup

#Get users that are part of a Machine's local Admin group
Find-GPOComputerAdmin -ComputerName <ComputerName>
```
- **Enum OUs:** 
```
Get-NetOU -FullData 
Get-NetGPO -GPOname <The GUID of the GPO>
```
- **Enum ACLs:** 
```
# Returns the ACLs associated with the specified account
Get-ObjectAcl -SamAccountName <AccountName> -ResolveGUIDs
Get-ObjectAcl -ADSprefix 'CN=Administrator, CN=Users' -Verbose

#Search for interesting ACEs
Invoke-ACLScanner -ResolveGUIDs

#Check the ACLs associated with a specified path (e.g smb share)
Get-PathAcl -Path "\\Path\Of\A\Share"
```
- **Enum Domain Trust:** 
```
Get-NetDomainTrust
Get-NetDomainTrust -Domain <Specific Domain>
```
- **Enum Forest Trust:** 
```
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Domains of Forest Enumeration
Get-NetForestDomain
Get-NetForestDomain Forest <ForestName>

#Map the Trust of the Forest
Get-NetForestTrust
Get-NetDomainTrust -Forest <ForestName>
```
- **User Hunting:** 
```
#Finds all machines on the current domain where the current user has local admin access
Find-LocalAdminAccess -Verbose

#Find local admins on all machines of the domain:
Invoke-EnumerateLocalAdmin -Verbose

#Find computers were a Domain Admin OR a spesified user has a session
Invoke-UserHunter
Invoke-UserHunter -GroupName "RDPUsers"
Invoke-UserHunter -Stealth

#Confirming admin access:
Invoke-UserHunter -CheckAccess
```
:heavy_exclamation_mark: **Priv Esc to Domain Admin with User Hunting:** \
I have local admin access on a machine -> A Domain Admin has a session on that machine -> I steal his token and impersonate him -> Profit!

### With AD Module

- **Get Current Domain:** `Get-ADDomain`
- **Enum Other Domains:** `Get-ADDomain -Identity <Domain>`
- **Get Domain SID:** `Get-DomainSID`
- **Get Domain Controlers:** 
```
Get-ADDomainController
Get-ADDomainController -Identity <DomainName>
```
- **Enumerate Domain Users:** 
```
Get-ADUser
-Filter * -Identity <user> -Properties *

#Get a spesific "string" on a user's attribute
Get-ADUser -Filter 'Description -like "*wtver*"' -Properties Description | select Name, Description
```
- **Enum Domain Computers:** 
```
Get-ADComputer -Filter * -Properties *
Get-ADGroup -Filter * 
```
- **Enum Domain Trust:** 
```
Get-ADTrust -Filter *
Get-ADTrust -Identity <Specific Domain>
```
- **Enum Forest Trust:** 
```
Get-ADForest
Get-ADForest -Identity <ForestName>

#Domains of Forest Enumeration
(Get-ADForest).Domains
```
## Local Privilege Escalation

## Lateral Movement

## Domain Persistence

## Domain Privilege Escalation

## Cross Forest Attacks

## Forest Persistence

## References
