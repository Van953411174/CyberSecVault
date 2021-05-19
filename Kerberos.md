# Kerberos
**Index**
- [[Kerberos#What is it|What is it?]]
- 


## What is it?
#Kerberos is a #SSO platform that uses #symmetric-key cryptography and requires trusted 3rd authorization to verify user identities.

Kerberos is the default authentication service of [[Active Directory]] #AD. 

**Kerberos Process**
![Kerberos Process](https://i.imgur.com/nnsV5NM.png)

**Great Youtube video giving high/mid lvl overview: **
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/qW361k3-BtU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Components & Terminology
-   **Ticket Granting Ticket (TGT)** - A ticket-granting ticket is an authentication ticket used to request service tickets from the TGS for specific resources from the domain.
-   **Key Distribution Center (KDC)** - The Key Distribution Center is a service for issuing TGTs and service tickets that consist of the Authentication Service and the Ticket Granting Service.
-   **Authentication Service (AS)** - The Authentication Service issues TGTs to be used by the TGS in the domain to request access to other machines and service tickets.
-   **Ticket Granting Service (TGS)** - The Ticket Granting Service takes the TGT and returns a ticket to a machine on the domain.  
-   **Service Principal Name (SPN)** - A Service Principal Name is an identifier given to a service instance to associate a service instance with a domain service account. Windows requires that services have a domain service account which is why a service needs an SPN set.
-   **KDC Long Term Secret Key (KDC LT Key)** \- The KDC key is based on the KRBTGT service account. It is used to encrypt the TGT and sign the PAC.
-   **Client Long Term Secret Key (Client LT Key)** \- The client key is based on the computer or service account. It is used to check the encrypted timestamp and encrypt the session key.
-   **Service Long Term Secret Key (Service LT Key)** \- The service key is based on the service account. It is used to encrypt the service portion of the service ticket and sign the PAC.
-   **Session Key** - Issued by the KDC when a TGT is issued. The user will provide the session key to the KDC along with the TGT when requesting a service ticket.
-   **Privilege Attribute Certificate (PAC)** - The PAC holds all of the user's relevant information, it is sent along with the TGT to the KDC to be signed by the Target LT Key and the KDC LT Key in order to validate the user.

\- TryHackMe 'Attacking Kerberos' Room
## Tickets Overview
The main ticket that you will see is a ticket-granting ticket these can come in various forms such as a .kirbi for [[Rubeus]] .ccache for #Impacket. The main ticket that you will see is a **.kirbi ticket.** A ticket is typically *base64 encoded* and can be used for various [[Kerberos#Attacks|attacks]]. The ticket-granting ticket is only used with the KDC in order to get service tickets. Once you give the TGT the server then gets the User details, session key, and then encrypts the ticket with the service account NTLM hash. Your TGT then gives the encrypted timestamp, session key, and the encrypted TGT. The KDC will then authenticate the TGT and give back a service ticket for the requested service. A normal TGT will only work with that given service account that is connected to it however a KRBTGT allows you to get any service ticket that you want allowing you to access anything on the domain that you want.

### Ticket Granting Ticket Contents
TGT is provided by the user to the KDC, in return the KDC validates the TGT and returns a service ticket. 
```
Insert mermaid LR flow or Seq diagram

```

![[Kerberos-TGT-Contents.png]]


### Service Ticket Contents
A service ticket contains two portions:
-   **Service Portion**: User Details, Session Key, Encrypts the ticket with the service account NTLM hash.
-   **User Portion:** Validity Timestamp, Session Key, Encrypts with the TGT session key.
![[Kerberos-service-ticket-contents.png]]

## Processes
### Basic Authentication Overview

![[Kerberos-auth-overview.png]]
**Basic Authentication Overview (-source THM)**

AS-REQ - 1.) The client requests an Authentication Ticket or Ticket Granting Ticket (TGT).

AS-REP - 2.) The Key Distribution Center verifies the client and sends back an encrypted TGT.

TGS-REQ - 3.) The client sends the encrypted TGT to the Ticket Granting Server (TGS) with the Service Principal Name (SPN) of the service the client wants to access.

TGS-REP - 4.) The Key Distribution Center (KDC) verifies the TGT of the user and that the user has access to the service, then sends a valid session key for the service to the client.

AP-REQ - 5.) The client requests the service and sends the valid session key to prove the user has access.

AP-REP - 6.) The service grants access

### AS-REQ w/ Pre-Authentication
The AS-REQ step in Kerberos authentication starts when a user requests a TGT from the KDC. In order to validate the user and create a TGT for the user, the KDC must follow these exact steps. The first step is for the user to encrypt a timestamp NT hash and send it to the AS. The KDC attempts to decrypt the timestamp using the NT hash from the user, if successful the KDC will issue a TGT as well as a session key for the user.

### KRBTGT
KRBTGT is the service account for the KDC that issues all of the tickets to the clients, whereas a TGT is a ticket to a service account issued by the KDC and can only access that service the TGT is from. 

KRBTGT impersonation allows for the creation of golden tickets as control of KRBTGT give you the ability to create service tickets for anything you want.
## Attacks
**Types and Privilege Requirements**
-   Kerbrute Enumeration - No domain access required 
-   Pass the Ticket - Access as a user to the domain required
-   Kerberoasting - Access as any user required
-   AS-REP Roasting - Access as any user required  
-   Golden Ticket - Full domain compromise (domain admin) required 
-   Silver Ticket - Service hash required 
-   Skeleton Key - Full domain compromise (domain admin) required

### Kerbrute User Enumeration
See [[kerbrute]] for usage.

By brute forcing Kerberos pre-auth you *should not* trigger an **account failed to log on** event which would be an alert for any blue team.  You can brute-force by only sending an single UDP frame to the KDC allowing you to enumerate users on the domain. 

### AS-REP Roasting
| Requirements                  | Success Dependencies |
| ----------------------------- | -------------------- |
| accounts w/ pre-auth disabled | Password strength    |
|                               |                      |
*can be non-service accounts*

AS-REP Roasting (or also listed as ASREPRoasting) occurs when a user account has the privilege "*Does not require Pre-Authentication*"". This means that account **does not** need to provide valid identification before requesting a Kerberos Ticket on a specified user account. 

It dumps the **krbasrep5** hashes of user accounts w/ pre-auth dsiabled. Unlike [[Kerberos#Kerberoasting|Kerberoasting]], these users do **NOT** have to be service accounts. The only requirement to be able to AS-REP Roast a user is the user must have pre-auth disabled. 

#### Overview
During pre-authentication, the users hash will be used to encrypt a timestamp that the domain controller will attempt to decrypt to validate that the right hash is being used and is not replaying a previous request. After validating the timestamp the KDC will then issue a TGT for the user. If pre-authentication is disabled you can request any authentication data for any user and the KDC will return an encrypted TGT that can be cracked offline because the KDC skips the step of validating that the user is really who they say that they are. - [TryHackMe](https://tryhackme.com/room/attackingkerberos)

#### Discovery
Some tools like Rubeus automatically carry out discovery of vulnerable accounts. Other tools like Impacket's [[GetNPUsers.py]] require enumeration of users before hand. 

[[kerbrute]] can be used to enumerate a valid set of usernames for GetNPUsers.py to query. 

#### Exploiting
Like Kerberoasting, there are a number of tools that can be used such as: [[kekeo]], [[Rubeus]], and Impacket's [[GetNPUsers.py].

The returned hash, type **Kerberos 5 AS-REP etype 23**, can be cracked with hashcat:
`hashcat -m 18200 -a 0 hash.txt <wordlist>.txt`

#### AS-REP Roasting w/ Rubeus
![[Rubeus#AS-REP Roasting]]

#### AS-REP Roasting w/ Impacket's GetNPUsers.py
[[GetNPUsers.py]] can query AS-REP Roastable accounts from the Key Distribution Center.  It does requires a list of valid users which can enumerated with [[kerbrute]].

![[GetNPUsers.py#Example Usage]]



### Kerberoasting
| Requirements                  | Success Dependencies                      |
| ----------------------------- | ----------------------------------------- |
| Service with a registered SPN | Password strength                |
|                               | If it is trackable                        |
|                               | Privileges of the cracked service account |                                        |

One of the most popular attacks, Kerberoasting allows a user to request a service ticket for any service w/ a registered SPN then use that ticket to crack the service password. 

#### Discovery
Tools like [[Bloodhound]] can be used to discover all Kerberoastable accounts, what kind of accounts they are, and what kind of connections they have to the rest of the domain. 

#### Exploiting
There are numerous tools that can do this: [[Rubeus#Kerberoasting]], [[kekeo]], [[Invoke-Kerberoast]], #Impacket's [[GetUserSPNs.py]]. 

The returned hash can be cracked with hashcat or equivalent:
`hashcat -m 13100 -a 0 <in-hash>.txt <wordlist>.txt`
#### Kerberoasting w/ Rubeus
![[Rubeus#Kerberoasting]]

#### Kerberoasting w/ Impacket's GetUsersSPNs.py
![[GetUserSPNs.py]]

#### Mitigation
-    Strong Service Passwords - If the service account passwords are strong then kerberoasting will be ineffective
-    Don't Make Service Accounts Domain Admins - Service accounts don't need to be domain admins, kerberoasting won't be as effective if you don't make service accounts domain admins.


### Pass-the-Ticket
*USER: LSASS -> Kerberos sequence diagram*
![[LSASS-Kerberos-sequence-diagram.png]]

Pass the ticket works by dumping the TGT from [[Windows - lsass.exe|LSASS]] memory with tools like [[Mimikatz]]. You can dump Kerberos tickets from LSASS memory just hashes. Dumping with #Mimikatz will give us a **.kirbi** ticket which can be used to gain domain admin **IF** if a domain admin ticket is in LSASS memory. 

**Uses**
- Privilege Escalation
	- Allows escalation to domain admin if you dump a domain admin ticket
	- Then impersonate that ticket using Mimikatz PTT (pass-the-ticket) attack allowing you to act as that domain admin
- Lateral Movement (if there are unsecured domain service account tickets laying around)

#### Exploiting
Exploiting with Mimikatz: [[Mimikatz#Pass-the-Ticket]]

#### Mitigation
- Don't let your domain admins log onto anything except the domain controller
	-  This is something so simple however a lot of domain admins still log onto low-level computers leaving tickets around that we can use to attack and move laterally with.



### Golden Ticket & Silver Ticket 
The approach to creating a Golden or Silver Ticket is basically the same. These tickets allow for elevated or uninhibited access to Kerberos services. Key difference is that **Silver** is limited to the service that is targeted and **Golden** has access to *any* Kerberos service.

- **Silver Ticket**
	- Can sometimes be better in engagements than a Golden Ticket because its more discreet. 
	- **Scenario**: Wanting to access the domain's SQL server, however your current compromised user doesn't have access to that server. You can find an accessible service account to get a foothold with by [[Kerberos#Kerberoasting|Kerberoasting]] that service, you can them dump the service hash and impersonate their TGT in order to request a service ticket for the SQL service form the KDC allowing you access to the domains SQL server.
 
- **Golden Ticket**
	- Accomplished through impersonation of the KRBTGT service account, which has the ability to create service tickets for any account, thus control means *YOU* have the ability to create service tickets for yourself. 

#### Exploit
Works by dumping the TGT for any user on the domain, preferable a domain admin, however for a *Golden* ticket you would dump the **krbtgt** ticket and for a *Silver* ticket you would dump any service or domain admin ticket. 

This provides you w/ the service/domain admin accounts SID (unique ID for each user account) as well as the #NTLM hash. 

Using Mimikatz you can use this information to create a TGT that impersonates the given service account information. 

See [[Mimikatz#Golden Silver Ticket Attacks]]
 
### Skeleton Keys (Backdoors)
 A Kerberos backdoor is more subtle than Golden/Silver Ticket attacks because it acts similar to a rootkit by implanting itself into the *memory* of the [[Forest - AD|domain forest]], allowing itself access to any of the machines with a *master password*. Living in memory it **does not persist** unless scripted or persisted by other tools/techniques/means.
 
 #### Overview
 Works by implanting a Skeleton Key (only works using Kerberos RC4 encryption) that abuses the way AS-REQ validates encrypted timestamps. These time stamps are encrypted with the users NT hash. The DC tries to decrypt the timestamp using both the NT hash and skeleton key NT hash (once its implanted) allowing you access to the domain forest
 
 #### Exploit
 *Default Skeleton Key hash is: 60BA4FCADC466C7A033C178194C03DF6
This makes the password **mimikatz** *
 ![[Mimikatz#Backdoors and Skeleton Keys]]
 
## Related 
[[THM - Attacking Kerberos]]

## Resources
-   [https://medium.com/@t0pazg3m/pass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a](https://medium.com/@t0pazg3m/pass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a)[](https://medium.com/@t0pazg3m/pass-the-ticket-ptt-attack-in-mimikatz-and-a-gotcha-96a5805e257a)
-   [https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat](https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat)[](https://ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat)
-   [https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1](https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1)[](https://posts.specterops.io/kerberoasting-revisited-d434351bd4d1)
-   [https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/](https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/)[](https://www.harmj0y.net/blog/redteaming/not-a-security-boundary-breaking-forest-trusts/)
-   [https://www.varonis.com/blog/kerberos-authentication-explained/](https://www.varonis.com/blog/kerberos-authentication-explained/)[](https://www.varonis.com/blog/kerberos-authentication-explained/)
-   [https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf](https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf)[](https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It-wp.pdf)
-   [https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf](https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf)[](https://www.sans.org/cyber-security-summit/archives/file/summit-archive-1493862736.pdf)
-   [https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf](https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf)