# Enumerating SMTP
Since email is often exposed to the wider net, #SMTP can be the endpoint used to establish a foothold to a network. #enumeration Enumeration it can also reveal potentially sensitive information like usernames or naming conventions, or even access to the server itself. 

## Tools
- MetaSploit
	- #metasploit has  `smtp_version` and `smtp_enum` modules that will #fingerprint and enumerate an SMTP server by scanning a range of IP addresses and determine the version of any mail servers it encounters. 
- #telnet
	- can be used to manually enumerate
- `smtp-user-enum`
	- tool for enumerating OS-lvl user accounts on #Solaris via SMTP service
	- enumeration performed by inspecting the responses to `VRFY`, `EXPN`, and `RCPT TO`
- `swaks`: Swiss Army Knife SMTP, all purpose SMTP transaction tester
	- scriptable SMTP test tool, essentially a wrapper around the protocol and variants allowing for easy testing of SMTP
	- really needs to be used in conjunction with other tools


## How to Enumerate
### Necessary Info
SMTP service has two internal commands that allow the users:
- `VRFY` confirming the names of valid users
- `EXPN` reveals the actual address of user's aliases and lsits of email (mailing lists)

### Process
Using metasploit and related SMTP modules alleviates a lot of the legwork for enumeration. *NOTE: Other tools like smtp-user-enum are better for OS-lvl user enum*

Using `smtp_version` [^1] after setting `RHOSTS` to your target IP returns the SMTP #banner.
Returns:
- server name
- SMTP variant
- server OS
- Mail Transfer Agent #MTA 

Example:
![[Pasted image 20210427183525.png]]


[^1]: full module path `auxiliary/scanner/smtp/smtp_version`

Using `smtp_enum`[^2] we can enumerate valid usernames through a bit of bruteforce and analysis. 
Required options:
- `RHOSTS`
- `RPORT` (default `25`)
- `UNIXONLY` (default `true`)
- `USER_FILE` (path to username list, default `/usr/share/metasploit-framework/data/wordlists/unix_users.txt`)

Returns:
- valid usernames

Example:
![[Pasted image 20210427183604.png]]

[^2]: full module path `auxiliary/scanner/smtp/smtp_enum`

## Continuing Topics
[[Exploiting SMTP]]