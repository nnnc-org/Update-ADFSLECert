# Update-ADFSLECert
This script uses Posh-ACME and Let's Encrypt to update ADFS service communications certificate
## How To Use:

### First Time Setup

If running Windows 2012 / Windows 2012 R2, you must first install PowerShell 5.1, available at [https://aka.ms/WMF5Download](https://aka.ms/WMF5Download).

First things first, you NEED to have a PAAccount setup before running this script. This script was only designed to handle renewals, not the entire process. To learn more, please see the documentation at [Posh-ACME](https://github.com/rmbolger/Posh-ACME). An example to setup an account:

```powershell
New-PACertificate -Domain sts.example.com -AcceptTOS -Contact me@example.com -DnsPlugin Cloudflare -PluginArgs @{CFAuthEmail="me@example.com";CFAuthKey='xxx'}

# After the above completes, run the following
$MainDomain = 'sts.example.com'

# the '-UseExisting' flag is useful when the certifcate is not yet expired
./Update-ADFSLECert.ps1 -MainDomain $MainDomain -UseExisting
```

Otherwise, to normally run it:

```powershell
./Update-ADFSLECert.ps1 -MainDomain $MainDomain
```

### Force Renewals

You can force a renewal with the '-ForceRenew' switch:

```powershell
./Update-ADFSLECert.ps1 -MainDomain $MainDomain -ForceRenew
```
#### Switch Mutual Exclusivity

The '-ForceRenew' and '-UseExisting' switches are mutually exclusive, with '-UseExisting' superceeding '-ForceRenew'.
