---
ms.date: 07/12/2021
title:  Compatibility Aliases
description: PowerShell has several aliases that allow UNIX and cmd.exe users to use familiar commands.
---
# Compatibility Aliases

PowerShell has several aliases that allow **UNIX** and **cmd.exe** users to use familiar commands.
The commands and their related PowerShell cmdlet and PowerShell alias are shown in the following
table:

|            cmd.exe command            | UNIX command | PowerShell cmdlet |             PowerShell alias              |
| ------------------------------------- | ------------ | ----------------- | ----------------------------------------- |
| **cd**, **chdir**                     | **cd**       | `Set-Location`    | `sl`, `cd`, `chdir`                       |
| **cls**                               | **clear**    | `Clear-Host`      | `cls` `clear`                             |
| **copy**                              | **cp**       | `Copy-Item`       | `cpi`, `cp`, `copy`                       |
| **del**, **erase**, **rd**, **rmdir** | **rm**       | `Remove-Item`     | `ri`, `del`, `erase`, `rd`, `rm`, `rmdir` |
| **dir**                               | **ls**       | `Get-ChildItem`   | `gci`, `dir`, `ls`                        |
| **echo**                              | **echo**     | `Write-Output`    | `write` `echo`                            |
| **md**                                | **mkdir**    | `New-Item`        | `ni`                                      |
| **move**                              | **mv**       | `Move-Item`       | `mi`, `move`, `mi`                        |
| **popd**                              | **popd**     | `Pop-Location`    | `popd`                                    |
|                                       | **pwd**      | `Get-Location`    | `gl`, `pwd`                               |
| **pushd**                             | **pushd**    | `Push-Location`   | `pushd`                                   |
| **ren**                               | **mv**       | `Rename-Item`     | `rni`, `ren`                              |
| **type**                              | **cat**      | `Get-Content`     | `gc`, `cat`, `type`                       |

To find all PowerShell aliases, use the [Get-Alias](xref:Microsoft.PowerShell.Utility.Get-Alias)
cmdlet. To display a cmdlet's aliases, use the **Definition** parameter and specify the cmdlet name.
Or, to find an alias's cmdlet name, use the **Name** parameter and specify the alias.

```powershell
Get-Alias -Definition Get-ChildItem
```

```Output
CommandType     Name
-----------     ----
Alias           dir -> Get-ChildItem
Alias           gci -> Get-ChildItem
Alias           ls -> Get-ChildItem
```

```powershell
Get-Alias -Name gci
```

```Output
CommandType     Name
-----------     ----
Alias           gci -> Get-ChildItem
```


## See Also

- [about_Aliases](/powershell/module/microsoft.powershell.core/about/about_aliases)
