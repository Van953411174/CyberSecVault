# Sysinternals - Miscellaneous Utilities
#Windows [[Windows - Sysinternals Suite|Sysinternals Suite]] category covering Files and Disk Utilities. This note is **non-exhaustive**

Link to full category tools: https://docs.microsoft.com/en-us/sysinternals/downloads/system-information

## BgInfo
> "It automatically displays relevant information about a Windows computer on the desktop's background, such as the computer name, IP address, service pack version, and more."

\- BgInfo [page](https://docs.microsoft.com/en-us/sysinternals/downloads/bginfo)

Handy utility if you manage multiple machines. Typically utilized by servers. When a user #RDP's into a server, the system information is displayed on the wallpaper to provide quick information about the server, such as the server's name. 

## RegJump
> "This little command-line applet takes a registry path and makes Regedit open to that path. It accepts root keys in standard (e.g. HKEY\_LOCAL\_MACHINE) and abbreviated form (e.g. HKLM)."

\- RegJump [page](https://docs.microsoft.com/en-us/sysinternals/downloads/regjump)

Normal operation of the #registry Editor requires drilling down to specific #registry_keys. RegJump will open the Registry Editor and automatically open the editor directly at the specified path, so you don't have to manually drill down. 

`regjump <registry_path>`

## Strings
> "Strings just scans the file you pass it for UNICODE (or ASCII) strings of a default length of 3 or more UNICODE (or ASCII) characters. Note that it works under Windows 95 as well."

\- Strings [page](https://docs.microsoft.com/en-us/sysinternals/downloads/strings)

