# admin-powershell

Windows PowerShell and Command Prompt notes for day-to-day administration. Each file is a list of short headings and fenced command blocks you can copy into a session. The notes cover local Windows, Active Directory, networking, printers, certificates, event logs, and Microsoft 365 (Exchange Online, Entra, and related modules).

There is no project build and there are no `.ps1` files. Paste one fenced block into a session. Prompts use `Read-Host` (and `Read-Host -AsSecureString` for passwords). Command Prompt blocks use `set /p`.

## Prerequisites

- Windows PowerShell on Windows for almost every block. The notes call Windows cmdlets, the registry, services, and `netsh`.
- An elevated PowerShell session for blocks that change the machine. `misc/general.md` opens one with `Start-Process -Verb RunAs`.
- The ActiveDirectory module for the `Get-AD*` and `Set-AD*` commands under `active-directory/` and in `misc/general.md`.
- WinRM on the remote computer for the remote service query in `diagnostics/services.md`.
- For the files under `o365/`, the modules those files install: `ExchangeOnlineManagement`, `AzureAD`, `MSOnline`, `Microsoft.Graph`, and `Az`. The `Connect-*` commands in those files sign in interactively.
- Command Prompt tools for `misc/command-prompt.md`: `nslookup`, `netsh`, `net`, System File Checker, `wmic`, and DISM.

These commands work in PowerShell 7 on Linux as well as on Windows. They come from `diagnostics/performance.md`:

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 -Property Id, ProcessName, CPU
```

```powershell
Get-PSDrive -PSProvider FileSystem | Where-Object Used -ne $null | Select-Object Name, @{Name="Used (GB)";Expression={[math]::Round($_.Used / 1GB, 2)}}, @{Name="Free (GB)";Expression={[math]::Round($_.Free / 1GB, 2)}} | Format-Table -AutoSize
```

Active Directory, Exchange, local accounts, Windows event logs, services, and firewall cmdlets are Windows-only.

## Install

This repository has no package manifest and no installer. Install the modules from the notes when you need that file.

Exchange Online (`o365/exchange/admin-commands.md`):

```powershell
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline
Connect-IPPSSession
Get-Command -Module ExchangeOnlineManagement
```

Entra / Azure AD (`o365/entra/user-account-commands.md`):

```powershell
Install-Module AzureAD -Scope CurrentUser
```

Microsoft 365 helpers (`o365/misc.md`):

```powershell
Install-Module MSOnline -Scope CurrentUser
Install-Module Microsoft.Graph -Scope CurrentUser
```

The same file installs `Az` with another user's credentials:

```powershell
$credential = Get-Credential
Start-Process -FilePath "powershell.exe" -Credential $credential -ArgumentList "-NoProfile","-Command","Install-Module -Name Az -Repository PSGallery -Force"
```

List Active Directory group commands once that module is available (`misc/general.md`):

```powershell
Get-Command -Module ActiveDirectory | findstr -i group
```

## Build

There is no build script, project file, or compile step. The notes are the product.

## Run

List the notes from the repository root:

```bash
find . -name '*.md' ! -name 'README.md' | sort
```

Before you run a saved script, `misc/general.md` relaxes policy for the current process:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process
```

Show help for a command (`misc/general.md`):

```powershell
Get-Help (Read-Host "Command")
```

Check the PowerShell version:

```powershell
$PSVersionTable.PSVersion
```

## Test

There is no test suite and no CI workflow. From the repository root, confirm the notes are present:

```bash
find . -name '*.md' ! -name 'README.md' | sort
```

The repository does not contain `.ps1` files. To syntax-check a fenced block, save that block as a `.ps1` file and parse it:

```powershell
$errs = $null
$tokens = $null
[void][System.Management.Automation.Language.Parser]::ParseFile("snippet.ps1", [ref]$tokens, [ref]$errs)
$errs
```

## Usage

1. Choose a file from the layout below.
2. Copy a single fenced block into Windows PowerShell, or into Command Prompt when the fence is marked `bat`.
3. Answer the `Read-Host` or `set /p` prompts. Passwords use `Read-Host -AsSecureString`.
4. Read the heading and the block first. Many blocks create or change users, groups, passwords, registry values, services, printers, mailbox permissions, or firewall exports.

## Layout

| Path | What the notes cover |
| --- | --- |
| `active-directory/enumerate.md` | Organizational units, computers, and other domain objects |
| `active-directory/group-commands.md` | Find groups, list members, and add or remove members |
| `active-directory/user-account-commands.md` | Create and update Active Directory users, passwords, and logon settings |
| `active-directory/password-audit.md` | List accounts by password age and force a password change |
| `local-accounts/user-account-commands.md` | Local users, local groups, and the local Administrators group |
| `local-accounts/mapped-drives.md` | Mapped drives for a user SID |
| `diagnostics/performance.md` | CPU, memory, uptime, disk space, and performance counters |
| `diagnostics/services.md` | List and disable services. Remote queries need WinRM |
| `security-events/event-logs.md` | Query and export Windows event logs |
| `security-events/firewall-rules.md` | Export firewall rules to CSV or XML |
| `security-events/certificates.md` | Read a signer certificate and export a certificate from a binary |
| `networking/adapters-and-dns.md` | Adapter bindings, DNS servers, connection profiles, and DNS TXT lookups |
| `networking/subnet-scans.md` | Check an IPv4 range for HTTP, an open port, and WinRM |
| `printers/printers.md` | Query, add, and clear printers |
| `o365/exchange/admin-commands.md` | Mailboxes, permissions, forwarding, journal rules, and message trace |
| `o365/entra/user-account-commands.md` | Microsoft Graph and Azure AD user lookups |
| `o365/misc.md` | License status, Microsoft Graph user lookup, and the `Az` module |
| `misc/general.md` | Execution policy, SMB, startup programs, file ACLs, domain join, and AD sync |
| `misc/command-prompt.md` | `nslookup`, Wi-Fi profiles, `net`, System File Checker, `wmic`, and DISM |
| `misc/registry.md` | Logon and Winlogon values, plus registry value types |
| `misc/browser-notifications.md` | Read and remove Chrome and Edge notification site entries |
| `misc/fix-windows.md` | Clear Windows Update, user, and Teams caches |

## License

This repository does not include a license file.
