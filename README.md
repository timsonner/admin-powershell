# admin-powershell

Windows PowerShell and Command Prompt notes for day-to-day administration. Each file is a list of short headings and fenced command blocks you can copy into a session. The notes cover local Windows, Active Directory, networking, printers, certificates, event logs, and Microsoft 365 (Exchange Online, Entra, and related modules).

## Prerequisites

- Windows PowerShell on Windows. The notes call Windows cmdlets, the registry, services, and `netsh`.
- An elevated PowerShell session for blocks that change the machine. `misc.md` opens one with `Start-Process -Verb RunAs`.
- The ActiveDirectory module for the `Get-AD*` and `Set-AD*` commands in `ad-enum.md`, `ad-group-commands.md`, `ad-user-account-commands.md`, `password-audit.md`, and `misc.md`.
- WinRM on the remote computer for the remote service query in `services.md`.
- For the files under `o365/`, the modules those files install: `ExchangeOnlineManagement`, `AzureAD`, `MSOnline`, `Microsoft.Graph`, and `Az`. The `Connect-*` commands in those files sign in interactively.
- Command Prompt tools for `not-powershell.md`: `nslookup`, `netsh`, `net`, System File Checker, and DISM.

## Install

This repository has no package manifest and no installer. Install the modules from the notes when you need that file.

Exchange Online (`o365/exchange/exchange-admin-commands.md`):

```powershell
Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser
Import-Module ExchangeOnlineManagement
Connect-ExchangeOnline
Connect-IPPSSession
Get-Command -Module ExchangeOnlineManagement
```

Entra / Azure AD (`o365/entra/entra-user-account-commands.md`):

```powershell
Install-Module AzureAD -Scope CurrentUser
```

Microsoft 365 helpers (`o365/misc.md`):

```powershell
Install-Module MSOnline -Scope CurrentUser
Install-Module Microsoft.Graph -Scope CurrentUser
```

The same file installs `Az` with the current user's credentials:

```powershell
$credential = Get-Credential
$command = "Install-Module -Name Az -Repository PSGallery -Force"
Start-Process -FilePath $command -Credential $credential
```

List Active Directory group commands once that module is available (`misc.md`):

```powershell
Get-Command -Module ActiveDirectory | findstr -i group
```

## Build

There is no build script, project file, or compile step. The notes are the product.

## Run

Clone the repository, open a note, and paste one fenced block into Windows PowerShell. Before you run a saved script, `misc.md` relaxes policy for the current process:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process
```

A read-only example from `diagnostics.md`:

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 -Property Id, ProcessName, CPU
```

List the note files from the repository root:

```bash
find . -name '*.md' ! -name 'README.md' | sort
```

## Test

There is no test suite and no CI workflow. From the repository root, confirm the notes are present:

```bash
find . -name '*.md' ! -name 'README.md' | sort
```

## Usage

1. Choose a file from the layout below.
2. Copy a single fenced block into Windows PowerShell.
3. Replace placeholders before you run the block. The notes use angle brackets, `CHANGE_ME`, and `Read-Host` prompts for names, paths, and identities.
4. Read the heading and the block first. Many blocks create or change users, groups, passwords, registry values, services, printers, mailbox permissions, or firewall exports.

`Get-Help` works for a specific command, as in `misc.md`:

```powershell
Get-Help Add-ADGroupMember
```

## Layout

| Path | What the notes cover |
| --- | --- |
| `ad-enum.md` | Organizational units, computers, and other domain objects |
| `ad-group-commands.md` | Find groups, list members, and add or remove members |
| `ad-user-account-commands.md` | Create and update Active Directory users, passwords, and logon settings |
| `password-audit.md` | List accounts by password age and force a password change |
| `local-user-account-commands.md` | Local users, local groups, and the local Administrators group |
| `diagnostics.md` | CPU, memory, uptime, disk space, and performance counters |
| `events-and-logging.md` | Query and export Windows event logs |
| `services.md` | List and disable services. Remote queries need WinRM |
| `networking.md` | Adapter bindings, DNS servers, connection profiles, and DNS TXT lookups |
| `subnet-scans.md` | Check an IPv4 range for HTTP, an open port, and WinRM |
| `export-firewall-rules.md` | Export firewall rules to CSV or XML |
| `registry.md` | Logon script and Winlogon values, plus registry value types |
| `certs.md` | Read a signer certificate and export a certificate from a binary |
| `printer-snippets.md` | Query, add, and clear printers |
| `browser-notifications.md` | Read and remove Chrome and Edge notification site entries |
| `fix-windows.md` | Clear Windows Update, user, and Teams caches |
| `get-mapped-drives.md` | List mapped drives for a user SID |
| `misc.md` | Execution policy, SMB, startup programs, file ACLs, domain join, and AD sync |
| `not-powershell.md` | `nslookup`, Wi-Fi profiles, `net`, System File Checker, and DISM |
| `o365/exchange/exchange-admin-commands.md` | Mailboxes, permissions, forwarding, journal rules, and message trace |
| `o365/entra/entra-user-account-commands.md` | Microsoft Graph and Azure AD user lookups |
| `o365/misc.md` | License status, Microsoft Graph user lookup, and the `Az` module |

## License

This repository does not include a license file.
