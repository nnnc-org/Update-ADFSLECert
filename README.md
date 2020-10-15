# Update-ADFSLECert
This script uses Posh-ACME and Let's Encrypt to update ADFS service communications certificate
## How To Use:

### First Time Setup

If running Windows 2012 / Windows 2012 R2, you must first install PowerShell 5.1, available at [https://aka.ms/WMF5Download](https://aka.ms/WMF5Download).

#### Install PowerShell Core

This script is designed to run on PowerShell Core.  To install PowerShell Core, visit [here](https://github.com/PowerShell/PowerShell/releases) to download an installer (the latest stable version will work).  

#### Install Posh-ACME module

Run command to install Posh-ACME:
```powershell
Install-Module -Name Posh-ACME -Scope AllUsers -AcceptLicense
```

#### Request initial certificate
The script is designed to handle the renewals automatically, so you need to request the initial certificate manually.  In PowerShell Core:

```powershell
New-PACertificate -Domain sts.example.com -AcceptTOS -Contact me@example.com -DnsPlugin Cloudflare -PluginArgs @{CFAuthEmail="me@example.com";CFAuthKey='xxx'}

# After the above completes, run the following
$MainDomain = 'sts.example.com'

# the '-UseExisting' flag is useful when the certifcate is not yet expired
./Update-ADFSLECert.ps1 -MainDomain $MainDomain -UseExisting
```
### Normal Use
To normally run it:

```powershell
./Update-ADFSLECert.ps1 -MainDomain $MainDomain
```

### Force Renewals

You can force a renewal with the '-ForceRenew' switch:

```powershell
./Update-ADFSLECert.ps1 -MainDomain $MainDomain -ForceRenew
```
### Other Notes

#### Switch Mutual Exclusivity

The '-ForceRenew' and '-UseExisting' switches are mutually exclusive, with '-UseExisting' superceeding '-ForceRenew'.

#### Logging

This script is set to automatically log the process and create a persistent log file in the same directory the script is located.  The name of the log file is UpdateADFS.txt

### Troubleshooting

####Windows 2012 R2

Windows 2012 R2 / ADFS 3.0 does not particularly like certificates with a Cryptography Next Generation (CNG) private key.  As a result, you will likely experience errors when attempting to run the script.  My experiences in troubleshooting this have been inconsistent.  However, after executing the initial run (with the -UseExsiting switch) and receiving an error message, I have found that I can usually get it to work following these steps:
- Use MMC to verify the Let's Encrypt certificate is successfully imported into the certificate store
- Copy the Let's Encrypt certificate thumbprint
- Use Windows Powershell (not Powershell 7) and manually set the certificate thumbprint, using the following command
  ```powershell
  Set-ADFSSSLCertificate -Thumbprint xxxxxx
  ```
- Reboot the server

This seems to help get around the CNG private key issue, for some reason.
