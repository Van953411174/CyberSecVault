# Kerberos

#Kerberos is a #SSO platform that uses #symmetric-key cryptographc and requires trusted 3rd authorization to verfiy user identities
![Kerberos Process](https://i.imgur.com/nnsV5NM.png)

Kerberos is the default authentication service of #AD. 

## Attacks
### ASREPRoasting
ASREPRoasting occurs when a user account has the privilege "*Does not require Pre-Authentication*"". This means that account **does not** need to provide valid identification before requesting a Kerberos Ticket on a specified user account. 

#### Exploiting
Part of #Impacket is a tool called [[GetNPUsers.py]] that can query ASReproastable accounts from the Key Distribution Center.  

The only thing thats necessary to query accounts is a valid set of usernames which we enumerated previously via [[kerbrute]]