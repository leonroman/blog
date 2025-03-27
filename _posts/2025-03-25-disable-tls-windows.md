---
layout: post
title: "Disable TLS 1.0 & 1.1 and enable TLS 1.2 on Windows"
categories: junk
author: rleon
tags: tls windows agent azure devops
---
# Prerequisites
## .Net

**Important:** For Visual Studio, you are required to use .NET Framework 4.5.2 version or higher for TLS 1.2.

Check .net version using cmd:

```bash
>reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\full" /v version

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\full
    version    REG_SZ    4.8.03761
```
or powershell terminal:

```bash
> Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP' -recurse |
>> Get-ItemProperty -name Version,Release -EA 0 |
>> Where { $_.PSChildName -match '^(?!S)\p{L}'} |
>> Select PSChildName, Version, Release

PSChildName Version   Release
----------- -------   -------
Client      4.8.03761  528049
Full        4.8.03761  528049
Client      4.0.0.0
```

# Security Protocol Support by OS Version
**Source:** https://learn.microsoft.com/en-us/security/engineering/solving-tls1-problem#figure-1-security-protocol-support-by-os-version

# Enable TLS 1.2
**Important:** If you run a Windows version higher that Windows 7 (WS2008 R2), you can skip this step.

**Source:** https://devblogs.microsoft.com/devops/azure-devops-services-to-require-tls-1-2

You need to execute the following script from the PowerShell to set the registry key for TLS 1.2. 
After running the script, you need to restart your machine.

```bash
  New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name SchUseStrongCrypto -Value 1 -PropertyType 'Dword' -Force | Out-Null 

  New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Name SystemDefaultTlsVersions -Value 1 -PropertyType 'Dword' -Force | Out-Null 

If ([System.Environment]::Is64BitOperatingSystem) { 

  New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name SchUseStrongCrypto -Value 1 -PropertyType 'Dword' -Force | Out-Null 

  New-ItemProperty -Path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Name SystemDefaultTlsVersions -Value 1 -PropertyType 'Dword' -Force | Out-Null 
}
```

# Disable TLS 1.0 & 1.1
**Source:** https://thesecmaster.com/blog/how-to-disable-tls-1-0-and-tls-1-1-on-windows-server

Run the below commands to create Registry entries.
 - TLS 1.0

```bash
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 0 /f 

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v DisabledByDefault /t REG_DWORD /d 1 /f

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client" /v Enabled /t REG_DWORD /d 0 /f 

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client" /v DisabledByDefault /t REG_DWORD /d 1 /f
```

 - TLS 1.1

```bash
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 0 /f 

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v DisabledByDefault /t REG_DWORD /d 1 /f

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client" /v Enabled /t REG_DWORD /d 0 /f 

reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client" /v DisabledByDefault /t REG_DWORD /d 1 /f
```

# Result

```bash
PS C:\> Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client'

Enabled           : 0
DisabledByDefault : 1
PSPath            : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client
PSParentPath      : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1
PSChildName       : Client
PSDrive           : HKLM
PSProvider        : Microsoft.PowerShell.Core\Registry



PS C:\> Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client'

Enabled           : 0
DisabledByDefault : 1
PSPath            : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client
PSParentPath      : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0
PSChildName       : Client
PSDrive           : HKLM
PSProvider        : Microsoft.PowerShell.Core\Registry
```