---
description: Describes the parameters that can be used with any cmdlet.
Locale: en-US
ms.date: 07/13/2021
no-loc: [Debug, Verbose, Confirm]
online version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_commonparameters?view=powershell-7.2&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about CommonParameters
---
# about_CommonParameters

## Short description

Describes the parameters that can be used with any cmdlet.

## Long description

The common parameters are a set of cmdlet parameters that you can use with any
cmdlet. They're implemented by PowerShell, not by the cmdlet developer, and
they're automatically available to any cmdlet.

You can use the common parameters with any cmdlet, but they might not have an
effect on all cmdlets. For example, if a cmdlet doesn't generate any verbose
output, using the **Verbose** common parameter has no effect.

The common parameters are also available on advanced functions that use the
**CmdletBinding** attribute or the **Parameter** attribute.

Several common parameters override system defaults or preferences that you set
by using the PowerShell preference variables. Unlike the preference variables,
the common parameters affect only the commands in which they're used.

For more information, see [about_Preference_Variables](./about_Preference_Variables.md).

The following list displays the common parameters. Their aliases are listed in
parentheses.

- **Debug** (db)
- **ErrorAction** (ea)
- **ErrorVariable** (ev)
- **InformationAction** (infa)
- **InformationVariable** (iv)
- **OutVariable** (ov)
- **OutBuffer** (ob)
- **PipelineVariable** (pv)
- **Verbose** (vb)
- **WarningAction** (wa)
- **WarningVariable** (wv)

The **Action** parameters are **ActionPreference** type values.
**ActionPreference** is an enumeration with the following values:

|        Name        | Value |
| ------------------ | ----- |
| `Break`            | 6     |
| `Suspend`          | 5     |
| `Ignore`           | 4     |
| `Inquire`          | 3     |
| `Continue`         | 2     |
| `Stop`             | 1     |
| `SilentlyContinue` | 0     |

You may use the name or the value with the parameter.

In addition to the common parameters, many cmdlets offer risk mitigation
parameters. Cmdlets that involve risk to the system or to user data usually
offer these parameters.

The risk mitigation parameters are:

- **WhatIf** (wi)
- **Confirm** (cf)

## Common parameter descriptions

### -Debug

Displays programmer-level detail about the operation done by the command. This
parameter works only when the command generates a debugging message. For
example, this parameter works when a command contains the `Write-Debug`
cmdlet.

```yaml
Type: SwitchParameter
Aliases: db

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

By default, debugging messages aren't displayed because the value of the
`$DebugPreference` variable is **SilentlyContinue**.

In interactive mode, the **Debug** parameter overrides the value of the
`$DebugPreference` variable for the current command, setting the value of
`$DebugPreference` to **Inquire**.

In non-interactive mode, the **Debug** parameter overrides the value of the
`$DebugPreference` variable for the current command, setting the value of
`$DebugPreference` to **Continue**.

`-Debug:$true` has the same effect as `-Debug`. Use `-Debug:$false` to
suppress the display of debugging messages when `$DebugPreference` isn't
**SilentlyContinue**, which is the default.

### -ErrorAction

Determines how the cmdlet responds to a non-terminating error from the command.
This parameter works only when the command generates a non-terminating error,
such as those from the `Write-Error` cmdlet.

```yaml
Type: ActionPreference
Aliases: ea
Accepted values: Break, Suspend, Ignore, Inquire, Continue, Stop, SilentlyContinue

Required: False
Position: Named
Default value: Depends on preference variable
Accept pipeline input: False
Accept wildcard characters: False
```

The **ErrorAction** parameter overrides the value of the `$ErrorActionPreference`
variable for the current command. Because the default value of the
`$ErrorActionPreference` variable is **Continue**, error messages are displayed
and execution continues unless you use the **ErrorAction** parameter.

The **ErrorAction** parameter has no effect on terminating errors (such as
missing data, parameters that aren't valid, or insufficient permissions) that
prevent a command from completing successfully.

`-ErrorAction:Break` Enters the debugger when an error occurs or an exception
is raised.

`-ErrorAction:Continue` displays the error message and continues executing the
command. `Continue` is the default.

`-ErrorAction:Ignore` suppresses the error message and continues executing the
command. Unlike **SilentlyContinue**, **Ignore** doesn't add the error message
to the `$Error` automatic variable. The **Ignore** value is introduced in
PowerShell 3.0.

`-ErrorAction:Inquire` displays the error message and prompts you for
confirmation before continuing execution. This value is rarely used.

`-ErrorAction:SilentlyContinue` suppresses the error message and continues
executing the command.

`-ErrorAction:Stop` displays the error message and stops executing the command.

`-ErrorAction:Suspend` is only available for workflows which aren't supported
in PowerShell 6 and beyond.

> [!NOTE]
> The **ErrorAction** parameter overrides, but does not replace the value of
> the `$ErrorAction` preference variable when the parameter is used in a
> command to run a script or function.

### -ErrorVariable

**ErrorVariable** stores error messages about the command in the specified
variable and in the `$Error` automatic variable. For more information, see
[about_Automatic_Variables](about_Automatic_Variables.md)

```yaml
Type: String
Aliases: ev

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

By default, new error messages overwrite error messages that are already stored
in the variable. To append the error message to the variable content, type a
plus sign (`+`) before the variable name.

For example, the following command creates the `$a` variable and then stores any
errors in it:

```powershell
Get-Process -Id 6 -ErrorVariable a
```

The following command adds any error messages to the `$a` variable:

```powershell
Get-Process -Id 2 -ErrorVariable +a
```

The following command displays the contents of `$a`:

```powershell
$a
```

You can use this parameter to create a variable that contains only error
messages from specific commands and does not affect the behavior of the
`$Error` automatic variable. The `$Error` automatic variable contains error
messages from all the commands in the session. You can use array notation, such
as `$a[0]` or `$error[1,2]` to refer to specific errors stored in the
variables.

> [!NOTE]
> The custom error variable contains all errors generated by the command,
> including errors from calls to nested functions or scripts.

### -InformationAction

Introduced in PowerShell 5.0. Within the command or script in which it's used,
the **InformationAction** common parameter overrides the value of the
`$InformationPreference` preference variable, which by default is set to
**SilentlyContinue**. When you use `Write-Information` in a script with
**InformationAction**, `Write-Information` values are shown depending on the
value of the **InformationAction** parameter. For more information about
`$InformationPreference`, see [about_Preference_Variables](./about_Preference_Variables.md).

```yaml
Type: ActionPreference
Aliases: ia
Accepted values: Break, Suspend, Ignore, Inquire, Continue, Stop, SilentlyContinue

Required: False
Position: Named
Default value: Depends on preference variable
Accept pipeline input: False
Accept wildcard characters: False
```

`-InformationAction:Break` Enters the debugger at an occurrence of the
`Write-Information` command.

`-InformationAction:Stop` stops a command or script at an occurrence of the
`Write-Information` command.

`-InformationAction:Ignore` suppresses the informational message and continues
running the command. Unlike **SilentlyContinue**, **Ignore** completely forgets the
informational message; it doesn't add the informational message to the
information stream.

`-InformationAction:Inquire` displays the informational message that you specify
in a `Write-Information` command, then asks whether you want to continue.

`-InformationAction:Continue` displays the informational message, and continues
running.

`-InformationAction:Suspend` isn't supported on PowerShell Core as it is only
available for workflows.

`-InformationAction:SilentlyContinue` no effect as the informational message
aren't (Default) displayed, and the script continues without interruption.

> [!NOTE]
> The **InformationAction** parameter overrides, but does not replace the
> value of the `$InformationAction` preference variable when the parameter
> is used in a command to run a script or function.

### -InformationVariable

Introduced in PowerShell 5.0. Within the command or script in which it's used,
the **InformationVariable** common parameter stores in a variable a string that
you specify by adding the `Write-Information` command. `Write-Information`
values are shown depending on the value of the **InformationAction** common
parameter; if you don't add the **InformationAction** common parameter,
`Write-Information` strings are shown depending on the value of the
`$InformationPreference` preference variable. For more information about
`$InformationPreference`, see [about_Preference_Variables](./about_Preference_Variables.md).

> [!NOTE]
> The information variable contains all information messages generated by the
> command, including information messages from calls to nested functions or
> scripts.

```yaml
Type: String
Aliases: iv

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -OutBuffer

Determines the number of objects to accumulate in a buffer before any objects
are sent through the pipeline. If you omit this parameter, objects are sent as
they're generated.

```yaml
Type: Int32
Aliases: ob

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

This resource management parameter is designed for advanced users. When you use
this parameter, PowerShell sends data to the next cmdlet in batches of
`OutBuffer + 1`.

The following example alternates displays between to `ForEach-Object` process
blocks that use the `Write-Host` cmdlet. The display alternates in batches of
2 or `OutBuffer + 1`.

```powershell
1..4 | ForEach-Object {
        Write-Host "$($_): First"; $_
      } -OutBuffer 1 | ForEach-Object {
                        Write-Host "$($_): Second" }
```

```Output
1: First
2: First
1: Second
2: Second
3: First
4: First
3: Second
4: Second
```

### -OutVariable

Stores output objects from the command in the specified variable in addition
to sending the output along the pipeline.

```yaml
Type: String
Aliases: ov

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

To add the output to the variable, instead of replacing any output that might
already be stored there, type a plus sign (`+`) before the variable name.

For example, the following command creates the `$out` variable and stores the
process object in it:

```powershell
Get-Process PowerShell -OutVariable out
```

The following command adds the process object to the `$out` variable:

```powershell
Get-Process iexplore -OutVariable +out
```

The following command displays the contents of the `$out` variable:

```powershell
$out
```

> [!NOTE]
> The variable created by the **OutVariable** parameter is a
> `[System.Collections.ArrayList]`.

### -PipelineVariable

**PipelineVariable** stores the value of the current pipeline element as a
variable, for any named command as it flows through the pipeline.

>[!NOTE]
> Advanced functions can have up to three script blocks: `begin`, `process`,
> and `end`. When using the **PipelineVariable** parameter with advanced
> functions, only values from the first defined script block are assigned to the
> variable as the function runs. For more information, see [Advanced functions](./about_functions_advanced.md).
> PowerShell 7.2 corrects this behavior.

```yaml
Type: String
Aliases: pv

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

Valid values are strings, the same as for any variable names.

The following is an example of how **PipelineVariable** works. In this example,
the **PipelineVariable** parameter is added to a `Foreach-Object` command to
store the results of the command in variables. A range of numbers, 1 to 10, are
piped into the first `Foreach-Object` command, the results of which are stored
in a variable named **Left**.

The results of the first `Foreach-Object` command are piped into a second
`Foreach-Object` command, which filters the objects returned by the first
`Foreach-Object` command. The results of the second command are stored in a
variable named **Right**.

In the third `Foreach-Object` command, the results of the first two
`Foreach-Object` piped commands, represented by the variables **Left** and
**Right**, are processed by using a multiplication operator. The command instructs
objects stored in the **Left** and **Right** variables to be multiplied, and
specifies that the results should be displayed as "Left range member * Right
range member = product".

```powershell
1..10 | Foreach-Object -PipelineVariable Left -Process { $_ } |
  Foreach-Object -PV Right -Process { 1..10 } |
  Foreach-Object -Process { "$Left * $Right = " + ($Left*$Right) }
```

```output
1 * 1 = 1
1 * 2 = 2
1 * 3 = 3
1 * 4 = 4
1 * 5 = 5
...
```

### -Verbose

Displays detailed information about the operation done by the command. This
information resembles the information in a trace or in a transaction log. This
parameter works only when the command generates a verbose message. For example,
this parameter works when a command contains the `Write-Verbose` cmdlet.

```yaml
Type: SwitchParameter
Aliases: vb

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

The **Verbose** parameter overrides the value of the `$VerbosePreference`
variable for the current command. Because the default value of the
`$VerbosePreference` variable is **SilentlyContinue**, verbose messages aren't
displayed by default.

`-Verbose:$true` has the same effect as `-Verbose`

`-Verbose:$false` suppresses the display of verbose messages. Use this parameter
when the value of `$VerbosePreference` isn't **SilentlyContinue** (the
default).

### -WarningAction

Determines how the cmdlet responds to a warning from the command. **Continue**
is the default value. This parameter works only when the command generates a
warning message. For example, this parameter works when a command contains the
`Write-Warning` cmdlet.

```yaml
Type: ActionPreference
Aliases: wa
Accepted values: Break, Suspend, Ignore, Inquire, Continue, Stop, SilentlyContinue

Required: False
Position: Named
Default value: Depends on preference variable
Accept pipeline input: False
Accept wildcard characters: False
```

The **WarningAction** parameter overrides the value of the
`$WarningPreference` variable for the current command. Because the default
value of the `$WarningPreference` variable is **Continue**, warnings are
displayed and execution continues unless you use the **WarningAction**
parameter.

`-WarningAction:Break` enters the debugger when a warning occurs.

`-WarningAction:Continue` displays the warning messages and continues executing
the command. `Continue` is the default.

`-WarningAction:Inquire` displays the warning message and prompts you for
confirmation before continuing execution. This value is rarely used.

`-WarningAction:SilentlyContinue` suppresses the warning message and continues
executing the command.

`-WarningAction:Stop` displays the warning message and stops executing the
command.

> [!NOTE]
> The **WarningAction** parameter overrides, but does not replace the value of
> the `$WarningAction` preference variable when the parameter is used in a
> command to run a script or function.

### -WarningVariable

Stores warnings about the command in the specified variable.

```yaml
Type: String
Aliases: wv

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

All generated warnings are saved in the variable even if the warnings aren't
displayed to the user.

To append the warnings to the variable content, instead of replacing any
warnings that might already be stored there, type a plus sign (`+`) before the
variable name.

For example, the following command creates the `$a` variable and then stores any
warnings in it:

```powershell
Get-Process -Id 6 -WarningVariable a
```

The following command adds any warnings to the `$a` variable:

```powershell
Get-Process -Id 2 -WarningVariable +a
```

The following command displays the contents of `$a`:

```powershell
$a
```

You can use this parameter to create a variable that contains only warnings from
specific commands. You can use array notation, such as `$a[0]` or `$warning[1,2]`
to refer to specific warnings stored in the variable.

> [!NOTE]
> The warning variable contains all warnings generated by the command,
> including warnings from calls to nested functions or scripts.

## Risk Management Parameter Descriptions

### -WhatIf

Displays a message that describes the effect of the command, instead of
executing the command.

```yaml
Type: SwitchParameter
Aliases: wi

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

The **WhatIf** parameter overrides the value of the `$WhatIfPreference` variable
for the current command. Because the default value of the `$WhatIfPreference`
variable is 0 (disabled), **WhatIf** behavior isn't done without the
**WhatIf** parameter. For more information, see [about_Preference_Variables](about_Preference_Variables.md)

`-WhatIf:$true` has the same effect as `-WhatIf`.

`-WhatIf:$false` suppresses the automatic WhatIf behavior that results when the
value of the `$WhatIfPreference` variable is 1.

For example, the following command uses the `-WhatIf` parameter in a
`Remove-Item` command:

```powershell
Remove-Item Date.csv -WhatIf
```

Instead of removing the item, PowerShell lists the operations it would
do and the items that would be affected. This command produces the
following output:

```output
What if: Performing operation "Remove File" on
Target "C:\ps-test\date.csv".
```

### -Confirm

Prompts you for confirmation before executing the command.

```yaml
Type: SwitchParameter
Aliases: cf

Required: False
Position: Named
Default value: Depends on preference variable
Accept pipeline input: False
Accept wildcard characters: False
```

The **Confirm** parameter overrides the value of the `$ConfirmPreference`
variable for the current command. The default value is true. For more
information, see [about_Preference_Variables](about_Preference_Variables.md)

`-Confirm:$true` has the same effect as `-Confirm`.

`-Confirm:$false` suppresses automatic confirmation, which occurs when the value
of `$ConfirmPreference` is less than or equal to the estimated risk of the
cmdlet.

For example, the following command uses the **Confirm** parameter with a
`Remove-Item` command. Before removing the item, PowerShell lists the
operations it would do and the items that would be affected, and asks for
approval.

```
PS C:\ps-test> Remove-Item tmp*.txt -Confirm

Confirm
Are you sure you want to perform this action?
Performing operation "Remove File" on Target " C:\ps-test\tmp1.txt
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend
[?] Help (default is "Y"):
```

The Confirm response options are as follows:

|      Response      |                           Result                            |
| ------------------ | ----------------------------------------------------------- |
| `Yes` (`Y`)        | Perform the action.                                         |
| `Yes to All` (`A`) | Perform all actions and suppress subsequent Confirm queries |
|                    | for this command.                                           |
| `No` (`N`):        | Do not perform the action.                                  |
| `No to All` (`L`): | Do not perform any actions and suppress subsequent Confirm  |
|                    | queries for this command.                                   |
| `Suspend` (`S`):   | Pause the command and create a temporary session.           |
| `Help` (`?`)       | Display help for these options.                             |

The **Suspend** option places the command on hold and creates a temporary
nested session in which you can work until you're ready to choose a **Confirm**
option. The command prompt for the nested session has two extra carets (>>) to
indicate that it's a child operation of the original parent command. You can
run commands and scripts in the nested session. To end the nested session and
return to the Confirm options for the original command, type "exit".

In the following example, the **Suspend** option (S) is used to halt a command
temporarily while the user checks the help for a command parameter. After
obtaining the needed information, the user types "exit" to end the nested prompt
and then selects the Yes (y) response to the Confirm query.

```
PS C:\ps-test> New-Item -ItemType File -Name Test.txt -Confirm

Confirm
Are you sure you want to perform this action?

Performing operation "Create File" on Target "Destination:
C:\ps-test\test.txt".
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default
is "Y"): s

PS C:\ps-test> Get-Help New-Item -Parameter ItemType

-ItemType <string>
Specifies the provider-specified type of the new item.

Required?                    false
Position?                    named
Default value
Accept pipeline input?       true (ByPropertyName)
Accept wildcard characters?  false

PS C:\ps-test> exit

Confirm
Are you sure you want to perform this action?
Performing operation "Create File" on Target "Destination: C:\ps-test\test
.txt".
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (defau
lt is "Y"): y

Directory: C:\ps-test

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
-a---         8/27/2010   2:41 PM          0 test.txt
```

## Keywords

about_Common_Parameters

## See also

[about_Preference_Variables](about_Preference_Variables.md)

[Write-Debug](xref:Microsoft.PowerShell.Utility.Write-Debug)

[Write-Warning](xref:Microsoft.PowerShell.Utility.Write-Warning)

[Write-Error](xref:Microsoft.PowerShell.Utility.Write-Error)

[Write-Verbose](xref:Microsoft.PowerShell.Utility.Write-Verbose)
