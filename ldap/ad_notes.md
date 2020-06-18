AD Notes
---

#### ADDS installation

```powershell
#Set-ExecutionPolicy#
Set-ExecutionPolicy remotesigned

#enable windows remote shell access#
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\WinRM\Service\WinRS" -name "AllowRemoteShellAccess" -Value "0" -PropertyType "DWORD" -Force| out-null

#install ADDS#
Install-windowsfeature AD-domain-services
Install-windowsfeature RSAT-ADDS-Tools
Install-windowsfeature RSAT-AD-AdminCenter
Install-windowsfeature RSAT-AD-PowerShell
Install-windowsfeature GPMC

Import-Module ADDSDeployment

Install-ADDSForest `
-CreateDnsDelegation:$false `
-DatabasePath "C:\Windows\NTDS" `
-DomainMode "Win2012R2" `
-DomainName "heylinux-ad.com" `
-DomainNetbiosName "HEYLINUX" `
-ForestMode "Win2012R2" `
-InstallDns:$true `
-LogPath "C:\Windows\NTDS" `
-NoRebootOnCompletion:$false `
-SysvolPath "C:\Windows\SYSVOL" `
-Force:$true

# Save above codes as powershell script adds_install.ps1
# Run adds_install.ps1 as administrator:
SafeModeAdministratorPassword: NotRea1_P_sS0rdT
Confirm SafeModeAdministratorPassword: NotRea1_P_sS0rdT
```

#### Search a AD group with recursive match

```bash
$ ldapsearch -p 389 -h ad.inc.heylinux.com -D "CN=CICD,OU=IT,OU=HEYLINUX,DC=heylinux,DC=com" -w "P_Ss0rdT" -b "DC=heylinux,DC=com" -s sub "(&(memberOf:1.2.840.113556.1.4.1941:=CN=SRE,OU=IT,OU=HEYLINUX,DC=heylinux,DC=com)(|(objectClass=user)(objectClass=group)))"
```
