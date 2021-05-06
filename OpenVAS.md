# OpenVAS 
#OpenVAS, or Open Vulnerability Assessment Scanner, is an application to scan endpoints and web applications to identify and detect vulnerabilities. Commonly used as part of mitigation solutions to quickly identify any gaps in production or development servers/applications. 

Is part of the Greenbone Vulnerability Management ( #GVM) Solution. Used for GreenBone Security Manager appliances and is a full-featured scan engine that executes a continuously updated and extended feed of Network Vulnerability Tests ( #NVT)

See [[GVM - Greenbone Vulnerability Management]] for more on the framework. 

-------------
**_NOTE_**: OpenVAS renamed to `gvm` in 2020. Some distros reference OpenVAS with `openvas` or `gvm` or a combination of both.

----------------

## Installation Options
OpenVAS comes preinstalled on Kali and Parrot, but Docker containers for it are also available.
### via Docker
To Run via #Docker: `docker run -d -p 443:443 --name openvas mikesplain/openvas`

This will pull the docker container (**INTERNET required**) and then run the container. Obviously you can download the container yourself and run it without pulling from repo. 

Once running, navigate to `https://127.0.0.1` in your web browser to access the OpenVAS interface. 

Default credentials are *admin::admin*

### via Installed Package
With OpenVAS installed by default on Parrot/Kali, you can also easily start the application from the command line.
 - `gvm-setup` - setup script
 - `gvm-feed-update` - signature updater
 - `gvmd` - Manager of the OpenVAS system
 - `gvm-manage-certs` - Manage cert infra for an OpenVAS installation

There are two additional utils w/ Parrot:
- `openvas-nasl` Executes a set of #NASL ( #Nessus Attack Scripting Language) scripts against a given target host.
- `openvas-nasl-lint` Linter for testing scripts for syntax errors

#### Setup
Before use you have to run the setup script. Its **_very_** long process, and an automatically generated password will be displayed for you. 
*SAVE SOMEWHERE SAFE*

##### Errors
It is likely that first time setup you'll encounter an error about #PostgreSQL version.
![[gvm-setup-error-postgres.png]]

This can be remediated rather easily, as odds are you do have the required version but older versions are in the way. 

1. First list out the current clusters w/: `pg_lsclusters` 
![[postgresql-ls-clusters.png]]
2. Next stop the running clusters with a version less than 13: [^1]
 `sudo systemctl stop postgresql@11-main && sudo systemctl stop postgresql@12-main` ![[postgresql-ls-clusters-after-stop.png]]
3. Then remove the old clusters, i.e. drop them: `sudo pg_dropcluster 11 main && sudo pg_dropcluster 12 main`
4. Lastly, verify the clusters have been removed (`pg_lsclusters`) and rerun *setup* (`sudo gvm-setup`. ![[pg-ls-clusters-after-drop.png]]

[^1]: Technically this can be skipped by using the `--stop` option with `sudo pg_dropcluster`. However its more graceful to use `systemctl` ahead of time. 

After setup completion, or after `gvm-start` there will be two listening TCP ports: 9390 and 9392, with the latter being the Web Interface accessible at:
`https://127.0.0.1:9392`

#### Resetting Password
The `gvmd` util can be used to create and remove users as well as reset their passwords. 

Example: 
```
root@kali:~# gvmmd --create-user=dookie  
User created with password 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyy'.  
root@kali:~# gvmmd --get-users  
admin  
dookie  
root@kali:~# gvmmd --user=dookie --new-password=s3cr3t  
root@kali:~# gvmmd --user=admin --new-password=sup3rs3cr3t
```

## Using OpenVAS
