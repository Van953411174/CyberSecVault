# Kerbrute - Kerberos Pre-auth Bruteforcing

Github: https://github.com/ropnop/kerbrute

By default `kerbrute` is mulithreaded at 10 threads, this can be changed with the `-t` option. 

Has three main commands:
-   **bruteuser** - Bruteforce a single user's password from a wordlist
-   **bruteforce** - Read username:password combos from a file or stdin and test them
-   **passwordspray** - Test a single password against a list of users
-   **userenum** - Enumerate valid domain usernames via Kerberos

A domain (`-d`) or a domain controller (`--dc`) must be specified. If a Domain Controller is not given the KDC will be looked up via DNS.

**NOTE** its helpful to add an entry to `/etc/hosts` so that the DNS can resolve. 
- E.g. If target is `10.10.173.126` with Domain Name `CONTROLLER.local` then entry would look like similar to:![[kerbrute-add-hosts-entry.png]]

## Example
`kerbrute userenum -d CONTROLLER.local --dc CONTROLLER.local usernames.txt`

