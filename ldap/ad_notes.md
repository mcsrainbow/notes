#### Search a AD group with recursive match

```bash
$ ldapsearch -p 389 -h ad.inc.heylinux.com -D "CN=CICD,OU=IT,OU=HEYLINUX,DC=heylinux,DC=com" -w "P_Ss0rdT" -b "DC=heylinux,DC=com" -s sub "(&(memberOf:1.2.840.113556.1.4.1941:=CN=SRE,OU=IT,OU=HEYLINUX,DC=heylinux,DC=com)(|(objectClass=user)(objectClass=group)))"
```
