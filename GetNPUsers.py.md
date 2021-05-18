<- [[Kerberos]]
# GetNPUsers.py
Part of the #Impacket, queries target domain for users with "*Do no require #Kerberos preauthentication*" set and export their TGTs for cracking


![[GetNPUsers.py-usage.png]]


## Example Usage
`GetNPUsers.py -dc-ip 10.10.219.29 spookysec.local/svc-admin -no-pass`
- AD domain spookysec.local
- Account svc-admin (discovered with [[kerbrute]])

Returns a **Kerberos 5 AS-REP etype 23** hash type, hashcat code 18200
![[GetNPUsers.py-example.png]]

