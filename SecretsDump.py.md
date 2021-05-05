# SecretsDump.py by Impacket
SecretsDump.py is a #Python tool by #Impacket that, like #Mimikatz, can extract secrets from targets. However, #secretsdump lives on the Network, rather than on the host like Mimikatz.

SecretsDump.py is routinely used in #red_team scenarios or by #threat_actors once access is accomplished to pivot, escalate, or just move into less likely to be noticed accounts. 


## Usage
Full tool help can be displayed by just running the script w/o params:
`secretsdump.py`

Following is the usage message:
![[secretsdump_py-usage-mssg.png]]

## Examples:
SecretsDump has a lot of options and can be used in a lot of different ways.
### Dumping Secrets from Domain Controller
Take for example a compromised #DC via a [[ZeroLogon]] attack that set the password to empty. 
SecretsDump.py can be leveraged to quickly dump lots of useful information from the server. 

`secretsdump.py -just-dc -no-pass DC01\$@<IP_ADDR>`
 - `DC01\$` NetBIOS name of Domain Controller, the `$` is escaped
 - `-just-dc` Extract only NTDS.DIT data (NTLM hashes and Kerberos keys)
 - `-no-pass` don't ask for password 

Output:
![[secretsdump_py-dump-dc.png]]