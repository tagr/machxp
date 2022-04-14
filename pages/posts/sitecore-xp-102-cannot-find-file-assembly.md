---
title: Error installing Sitecore XP 10.2 using Sitecore Install Assistant - XConnectXP0_InstallWDP Could not load file or assembly Microsoft.Identity.Client
date: 2022/4/13
description: Sitecore Install Assistant (SIA) keeps failing during XConnect installation due to missing DLL
tag: Sitecore,XP,Error,SIA,XConnect,Install
author: Andy Merhaut
---

# Error installing Sitecore XP 10.2 using Sitecore Install Assistant - XConnectXP0_InstallWDP Could not load file or assembly Microsoft.Identity.Client

1. Download [Microsoft.Identity.Client 4.22.0.0](https://www.nuget.org/packages/Microsoft.Identity.Client/4.22.0) from nuget.org. The `.nupkg` is a zip archive, so I copied the DLL to a temporary folder: `c:\sitecore10`.
2. From Visual Studio in Administrator mode, open [Developer PowerShell](https://docs.microsoft.com/en-us/visualstudio/ide/reference/command-prompt-powershell?view=vs-2022). Change to the `c:\sitecore10` directory.
3. Enter `gacutil -i Microsoft.Identity.Client.dll`

4. A message should appear, *Assembly successfully added to the cache*. Re-run the Sitecore Install Assistant

![Add Microsoft.Identity.Client to Global Assembly Cache (GAC)](/images/sitecore-xp-102-cannot-find-file-assembly.png "Add Microsoft.Identity.Client to Global Assembly Cache (GAC) from Visual Studio Developer PowerShell")

### Sitecore Install Assistant log

```
[-------------- XConnectXP0_InstallWDP : WebDeploy ---------------------------]
[WebDeploy]:[Path] C:\Program Files\iis\Microsoft Web Deploy V3\msdeploy.exe
Error Code: ERROR_EXECUTING_METHOD

More Information: The type initializer for 'Microsoft.Data.SqlClient.SqlAuthenticationProviderManager' threw an exception.

Could not load file or assembly 'Microsoft.Identity.Client, Version=4.22.0.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae' or one of its dependencies. The system cannot find the file specified.
Learn more at: https://go.microsoft.com/fwlink/?LinkId=221672#ERROR_EXECUTING_METHOD.

Error: The type initializer for 'Microsoft.Data.SqlClient.SqlAuthenticationProviderManager' threw an exception.
Error: Could not load file or assembly 'Microsoft.Identity.Client, Version=4.22.0.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae' or one of its dependencies. The system cannot find the file specified.
Error count: 1.
Command C:\Program Files\iis\Microsoft Web Deploy V3\msdeploy.exe returned a non-zero exit code - (-1)
[TIME] 00:02:42
Command C:\Program Files\iis\Microsoft Web Deploy V3\msdeploy.exe returned a non-zero exit code - (-1)
```