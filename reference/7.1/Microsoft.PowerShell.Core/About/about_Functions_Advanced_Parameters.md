---
description:  Explains how to add parameters to advanced functions.
keywords: powershell,cmdlet
Locale: en-US
ms.date: 07/12/2021
online version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_functions_advanced_parameters?view=powershell-7.1&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about Functions Advanced Parameters
---

# about_Functions_Advanced_Parameters

## Short description

Explains how to add parameters to advanced functions.

## Long description

You can add parameters to the advanced functions that you write, and use
parameter attributes and arguments to limit the parameter values that function
users submit with the parameter.

The parameters that you add to your function are available to users in addition
to the common parameters that PowerShell adds automatically to all cmdlets and
advanced functions. For more information about the PowerShell common
parameters, see [about_CommonParameters](about_CommonParameters.md).

Beginning in PowerShell 3.0, you can use splatting with `@Args` to represent
the parameters in a command. Splatting is valid on simple and advanced
functions. For more information, see [about_Functions](about_Functions.md) and
[about_Splatting](about_Splatting.md).

## Type conversion of parameter values

When you supply strings as arguments to parameters that expect a different
type, PowerShell implicitly converts the strings to the parameter target type.
Advanced functions perform culture-invariant parsing of parameter values.

By contrast, a culture-sensitive conversion is performed during parameter
binding for compiled cmdlets.

In this example, we create a cmdlet and a script function that take a
`[datetime]` parameter. The current culture is changed to use German settings.
A German-formatted date is passed to the parameter.

```powershell
# Create a cmdlet that accepts a [datetime] argument.
Add-Type @'
  using System;
  using System.Management.Automation;
  [Cmdlet("Get", "Date_Cmdlet")]
  public class GetFooCmdlet : Cmdlet {

    [Parameter(Position=0)]
    public DateTime Date { get; set; }

    protected override void ProcessRecord() {
      WriteObject(Date);
    }
  }
'@ -PassThru | % Assembly | Import-Module

[cultureinfo]::CurrentCulture = 'de-DE'
$dateStr = '19-06-2018'

Get-Date_Cmdlet $dateStr
```

```Output
Dienstag, 19. Juni 2018 00:00:00
```

As shown above, cmdlets use culture-sensitive parsing to convert the string.

```powershell
# Define an equivalent function.
function Get-Date_Func {
  param(
    [DateTime] $Date
  )
  process {
    $Date
  }
}

[cultureinfo]::CurrentCulture = 'de-DE'

# This German-format date string doesn't work with the invariant culture.
# E.g., [datetime] '19-06-2018' breaks.
$dateStr = '19-06-2018'

Get-Date_Func $dateStr
```

Advanced functions use culture-invariant parsing, which results in the
following error.

```Output
Get-Date_Func: Cannot process argument transformation on parameter 'Date'.
Cannot convert value "19-06-2018" to type "System.DateTime". Error: "String
'19-06-2018' was not recognized as a valid DateTime."
```

## Static parameters

Static parameters are parameters that are always available in the function.
Most parameters in PowerShell cmdlets and scripts are static parameters.

The following example shows the declaration of a **ComputerName** parameter
that has the following characteristics:

- It's mandatory (required).
- It takes input from the pipeline.
- It takes an array of strings as input.

```powershell
Param(
    [Parameter(Mandatory=$true,
    ValueFromPipeline=$true)]
    [String[]]
    $ComputerName
)
```

## Attributes of parameters

This section describes the attributes that you can add to function parameters.

All attributes are optional. However, if you omit the **CmdletBinding**
attribute, then to be recognized as an advanced function, the function must
include the **Parameter** attribute.

You can add one or multiple attributes in each parameter declaration. There's
no limit to the number of attributes that you can add to a parameter
declaration.

### Parameter attribute

The **Parameter** attribute is used to declare the attributes of function
parameters.

The **Parameter** attribute is optional, and you can omit it if none of the
parameters of your functions need attributes. But, to be recognized as an
advanced function, rather than a simple function, a function must have either
the **CmdletBinding** attribute or the **Parameter** attribute, or both.

The **Parameter** attribute has arguments that define the characteristics of
the parameter, such as whether the parameter is mandatory or optional.

Use the following syntax to declare the **Parameter** attribute, an argument,
and an argument value. The parentheses that enclose the argument and its value
must follow **Parameter** with no intervening space.

```powershell
Param(
    [Parameter(Argument=value)]
    $ParameterName
)
```

Use commas to separate arguments within the parentheses. Use the following
syntax to declare two arguments of the **Parameter** attribute.

```powershell
Param(
    [Parameter(Argument1=value1,
    Argument2=value2)]
)
```

The boolean argument types of the **Parameter** attribute default to **False**
when omitted from the **Parameter** attribute. Set the argument value to
`$true` or just list the argument by name. For example, the following
**Parameter** attributes are equivalent.

```powershell
Param(
    [Parameter(Mandatory=$true)]
)

# Boolean arguments can be defined using this shorthand syntax

Param(
    [Parameter(Mandatory)]
)
```

If you use the **Parameter** attribute without arguments, as an alternative to
using the **CmdletBinding** attribute, the parentheses that follow the
attribute name are still required.

```powershell
Param(
    [Parameter()]
    $ParameterName
)
```

#### Mandatory argument

The `Mandatory` argument indicates that the parameter is required. If this
argument isn't specified, the parameter is optional.

The following example declares the **ComputerName** parameter. It uses the
`Mandatory` argument to make the parameter mandatory.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [String[]]
    $ComputerName
)
```

#### Position argument

The `Position` argument determines whether the parameter name is required when
the parameter is used in a command. When a parameter declaration includes the
`Position` argument, the parameter name can be omitted and PowerShell
identifies the unnamed parameter value by its position, or order, in the list
of unnamed parameter values in the command.

If the `Position` argument isn't specified, the parameter name, or a parameter
name alias or abbreviation, must precede the parameter value whenever the
parameter is used in a command.

By default, all function parameters are positional. PowerShell assigns position
numbers to parameters in the order in which the parameters are declared in the
function. To disable this feature, set the value of the `PositionalBinding`
argument of the **CmdletBinding** attribute to `$False`. The `Position`
argument takes precedence over the value of the `PositionalBinding` argument of
the **CmdletBinding** attribute. For more information, see `PositionalBinding`
in [about_Functions_CmdletBindingAttribute](about_Functions_CmdletBindingAttribute.md).

The value of the `Position` argument is specified as an integer. A position
value of **0** represents the first position in the command, a position value
of **1** represents the second position in the command, and so on.

If a function has no positional parameters, PowerShell assigns positions to
each parameter based on the order in which the parameters are declared.
However, as a best practice, don't rely on this assignment. When you want
parameters to be positional, use the `Position` argument.

The following example declares the **ComputerName** parameter. It uses the
`Position` argument with a value of **0**. As a result, when `-ComputerName` is
omitted from command, its value must be the first or only unnamed parameter
value in the command.

```powershell
Param(
    [Parameter(Position=0)]
    [String[]]
    $ComputerName
)
```

#### ParameterSetName argument

The `ParameterSetName` argument specifies the parameter set to which a
parameter belongs. If no parameter set is specified, the parameter belongs to
all the parameter sets defined by the function. Therefore, to be unique, each
parameter set must have at least one parameter that isn't a member of any other
parameter set.

> [!NOTE]
> For a cmdlet or function, there is a limit of 32 parameter sets.

The following example declares a **ComputerName** parameter in the `Computer`
parameter set, a **UserName** parameter in the `User` parameter set, and a
**Summary** parameter in both parameter sets.

```powershell
Param(
    [Parameter(Mandatory=$true,
    ParameterSetName="Computer")]
    [String[]]
    $ComputerName,

    [Parameter(Mandatory=$true,
    ParameterSetName="User")]
    [String[]]
    $UserName,

    [Parameter(Mandatory=$false)]
    [Switch]
    $Summary
)
```

You can specify only one `ParameterSetName` value in each argument and only one
`ParameterSetName` argument in each **Parameter** attribute. To indicate that a
parameter appears in more than one parameter set, add additional **Parameter**
attributes.

The following example explicitly adds the **Summary** parameter to the
`Computer` and `User` parameter sets. The **Summary** parameter is optional in
the `Computer` parameter set and mandatory in the `User` parameter set.

```powershell
Param(
    [Parameter(Mandatory=$true,
    ParameterSetName="Computer")]
    [String[]]
    $ComputerName,

    [Parameter(Mandatory=$true,
    ParameterSetName="User")]
    [String[]]
    $UserName,

    [Parameter(Mandatory=$false, ParameterSetName="Computer")]
    [Parameter(Mandatory=$true, ParameterSetName="User")]
    [Switch]
    $Summary
)
```

For more information about parameter sets, see [About Parameter Sets](about_parameter_sets.md).

#### ValueFromPipeline argument

The `ValueFromPipeline` argument indicates that the parameter accepts input
from a pipeline object. Specify this argument if the function accepts the
entire object, not just a property of the object.

The following example declares a **ComputerName** parameter that's mandatory
and accepts an object that's passed to the function from the pipeline.

```powershell
Param(
    [Parameter(Mandatory=$true,
    ValueFromPipeline=$true)]
    [String[]]
    $ComputerName
)
```

#### ValueFromPipelineByPropertyName argument

The `ValueFromPipelineByPropertyName` argument indicates that the parameter
accepts input from a property of a pipeline object. The object property must
have the same name or alias as the parameter.

For example, if the function has a **ComputerName** parameter, and the piped
object has a **ComputerName** property, the value of the **ComputerName**
property is assigned to the function's **ComputerName** parameter.

The following example declares a **ComputerName** parameter that's mandatory
and accepts input from the object's **ComputerName** property that's passed to
the function through the pipeline.

```powershell
Param(
    [Parameter(Mandatory=$true,
    ValueFromPipelineByPropertyName=$true)]
    [String[]]
    $ComputerName
)
```

> [!NOTE]
> A typed parameter that accepts pipeline input (`by Value`) or
> (`by PropertyName`) enables use of _delay-bind_ script blocks on the
> parameter.
>
> The _delay-bind_ script block is run automatically during
> **ParameterBinding**. The result is bound to the parameter. Delay binding
> does not work for parameters defined as type `ScriptBlock` or
> `System.Object`. The script block is passed through _without_ being invoked.
>
> You can read about _delay-bind_ script blocks here [about_Script_Blocks.md](about_Script_Blocks.md).

#### ValueFromRemainingArguments argument

The `ValueFromRemainingArguments` argument indicates that the parameter accepts
all the parameter's values in the command that aren't assigned to other
parameters of the function.

The following example declares a **Value** parameter that's mandatory and a
**Remaining** parameter that accepts all the remaining parameter values that
are submitted to the function.

```powershell
function Test-Remainder
{
     param(
         [string]
         [Parameter(Mandatory = $true, Position=0)]
         $Value,
         [string[]]
         [Parameter(Position=1, ValueFromRemainingArguments)]
         $Remaining)
     "Found $($Remaining.Count) elements"
     for ($i = 0; $i -lt $Remaining.Count; $i++)
     {
        "${i}: $($Remaining[$i])"
     }
}
Test-Remainder first one,two
```

```Output
Found 2 elements
0: one
1: two
```

> [!NOTE]
> Prior to PowerShell 6.2, the **ValueFromRemainingArguments** collection was
> joined as single entity under index **0**.

#### HelpMessage argument

The `HelpMessage` argument specifies a string that contains a brief description
of the parameter or its value. PowerShell displays this message in the prompt
that appears when a mandatory parameter value is missing from a command. This
argument has no effect on optional parameters.

The following example declares a mandatory **ComputerName** parameter and a
help message that explains the expected parameter value.

If there is no other [comment-based help](./about_comment_based_help.md) syntax
for the function (for example, `.SYNOPSIS`) then this message also shows up in
`Get-Help` output.

```powershell
Param(
    [Parameter(Mandatory=$true,
    HelpMessage="Enter one or more computer names separated by commas.")]
    [String[]]
    $ComputerName
)
```

### Alias attribute

The **Alias** attribute establishes an alternate name for the parameter.
There's no limit to the number of aliases that you can assign to a parameter.

The following example shows a parameter declaration that adds the **CN** and
**MachineName** aliases to the mandatory **ComputerName** parameter.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [Alias("CN","MachineName")]
    [String[]]
    $ComputerName
)
```

### SupportsWildcards attribute

The **SupportsWildcards** attribute is used to indicate that the parameter
accepts wildcard values. The following example shows a parameter declaration
for a mandatory **Path** parameter that supports wildcard values.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [SupportsWildcards()]
    [String[]]
    $Path
)
```

Using this attribute does not automatically enable wildcard support. The cmdlet
developer must implement the code to handle the wildcard input. The wildcards
supported can vary according to the underlying API or PowerShell provider. For
more information, see [about_Wildcards](about_Wildcards.md).

### Parameter and variable validation attributes

Validation attributes direct PowerShell to test the parameter values that users
submit when they call the advanced function. If the parameter values fail the
test, an error is generated and the function isn't called. Parameter validation
is only applied to the input provided and any other values like default values
are not validated.

You can also use the validation attributes to restrict the values that
users can specify for variables. When you use a type converter along with a
validation attribute, the type converter has to be defined before the attribute.

```powershell
[int32][AllowNull()] $number = 7
```

### AllowNull validation attribute

The **AllowNull** attribute allows the value of a mandatory parameter to be
`$null`. The following example declares a hashtable **ComputerInfo** parameter
that can have a **null** value.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [AllowNull()]
    [hashtable]
    $ComputerInfo
)
```

> [!NOTE]
> The **AllowNull** attribute doesn't work if the type converter is set to
> string as the string type will not accept a null value. You can use the
> **AllowEmptyString** attribute for this scenario.

### AllowEmptyString validation attribute

The **AllowEmptyString** attribute allows the value of a mandatory parameter to
be an empty string (`""`). The following example declares a **ComputerName**
parameter that can have an empty string value.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [AllowEmptyString()]
    [String]
    $ComputerName
)
```

### AllowEmptyCollection validation attribute

The **AllowEmptyCollection** attribute allows the value of a mandatory
parameter to be an empty collection `@()`. The following example declares a
**ComputerName** parameter that can have an empty collection value.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [AllowEmptyCollection()]
    [String[]]
    $ComputerName
)
```

### ValidateCount validation attribute

The **ValidateCount** attribute specifies the minimum and maximum number of
parameter values that a parameter accepts. PowerShell generates an error if the
number of parameter values in the command that calls the function is outside
that range.

The following parameter declaration creates a **ComputerName** parameter that
takes one to five parameter values.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateCount(1,5)]
    [String[]]
    $ComputerName
)
```

### ValidateLength validation attribute

The **ValidateLength** attribute specifies the minimum and maximum number of
characters in a parameter or variable value. PowerShell generates an error if
the length of a value specified for a parameter or a variable is outside of the
range.

In the following example, each computer name must have one to ten characters.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateLength(1,10)]
    [String[]]
    $ComputerName
)
```

In the following example, the value of the variable `$number` must be a minimum
of one character in length, and a maximum of ten characters.

```powershell
[Int32][ValidateLength(1,10)]$number = '01'
```

> [!NOTE]
> In this example, the value of `01` is wrapped in single quotes. The
> **ValidateLength** attribute won't accept a number without being wrapped in
> quotes.

### ValidatePattern validation attribute

The **ValidatePattern** attribute specifies a regular expression that's
compared to the parameter or variable value. PowerShell generates an error if
the value doesn't match the regular expression pattern.

In the following example, the parameter value must contain a four-digit number,
and each digit must be a number zero to nine.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidatePattern("[0-9][0-9][0-9][0-9]")]
    [String[]]
    $ComputerName
)
```

In the following example, the value of the variable `$number` must be exactly a
four-digit number, and each digit must be a number zero to nine.

```powershell
[Int32][ValidatePattern("^[0-9][0-9][0-9][0-9]$")]$number = 1111
```

### ValidateRange validation attribute

The **ValidateRange** attribute specifies a numeric range or a
**ValidateRangeKind** enum value for each parameter or variable value.
PowerShell generates an error if any value is outside that range.

The **ValidateRangeKind** enum allows for the following values:

- **Positive** - A number greater than zero.
- **Negative** - A number less than zero.
- **NonPositive** - A number less than or equal to zero.
- **NonNegative** - A number greater than or equal to zero.

In the following example, the value of the **Attempts** parameter must be
between zero and ten.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateRange(0,10)]
    [Int]
    $Attempts
)
```

In the following example, the value of the variable `$number` must be between
zero and ten.

```powershell
[Int32][ValidateRange(0,10)]$number = 5
```

In the following example, the value of the variable `$number` must be greater
than zero.

```powershell
[Int32][ValidateRange("Positive")]$number = 1
```

### ValidateScript validation attribute

The **ValidateScript** attribute specifies a script that is used to validate a
parameter or variable value. PowerShell pipes the value to the script, and
generates an error if the script returns `$false` or if the script throws an
exception.

When you use the **ValidateScript** attribute, the value that's being validated
is mapped to the `$_` variable. You can use the `$_` variable to refer to the
value in the script.

In the following example, the value of the **EventDate** parameter must be
greater than or equal to the current date.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateScript({$_ -ge (Get-Date)})]
    [DateTime]
    $EventDate
)
```

In the following example, the value of the variable `$date` must be greater
than or equal to the current date and time.

```powershell
[DateTime][ValidateScript({$_ -ge (Get-Date)})]$date = (Get-Date)
```

> [!NOTE]
> If you use **ValidateScript**, you cannot pass a `$null` value to the
> parameter. When you pass a null value **ValidateScript** can't validate the
> argument.

### ValidateSet attribute

The **ValidateSet** attribute specifies a set of valid values for a parameter
or variable and enables tab completion. PowerShell generates an error if a
parameter or variable value doesn't match a value in the set. In the following
example, the value of the **Detail** parameter can only be Low, Average, or
High.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateSet("Low", "Average", "High")]
    [String[]]
    $Detail
)
```

In the following example, the value of the variable `$flavor` must be either
Chocolate, Strawberry, or Vanilla.

```powershell
[ValidateSet("Chocolate", "Strawberry", "Vanilla")]
[String]$flavor = "Strawberry"
```

The validation occurs whenever that variable is assigned even within the
script. For example, the following results in an error at runtime:

```powershell
Param(
    [ValidateSet("hello", "world")]
    [String]$Message
)

$Message = "bye"
```

#### Dynamic validateSet values

You can use a **Class** to dynamically generate the values for **ValidateSet**
at runtime. In the following example, the valid values for the variable
`$Sound` are generated via a **Class** named **SoundNames** that checks three
filesystem paths for available sound files:

```powershell
Class SoundNames : System.Management.Automation.IValidateSetValuesGenerator {
    [String[]] GetValidValues() {
        $SoundPaths = '/System/Library/Sounds/',
            '/Library/Sounds','~/Library/Sounds'
        $SoundNames = ForEach ($SoundPath in $SoundPaths) {
            If (Test-Path $SoundPath) {
                (Get-ChildItem $SoundPath).BaseName
            }
        }
        return [String[]] $SoundNames
    }
}
```

The `[SoundNames]` class is then implemented as a dynamic **ValidateSet** value
as follows:

```powershell
Param(
    [ValidateSet([SoundNames])]
    [String]$Sound
)
```

> [!NOTE]
> The `IValidateSetValuesGenerator` class was introduced in PowerShell 6.0

### ValidateNotNull validation attribute

The **ValidateNotNull** attribute specifies that the parameter value can't be
`$null`. PowerShell generates an error if the parameter value is `$null`.

The **ValidateNotNull** attribute is designed to be used when the parameter is
optional and the type is undefined or has a type converter that can't
implicitly convert a null value like **object**. If you specify a type that
that will implicitly convert a null value such as a **string**, the null value
is converted to an empty string even when using the **ValidateNotNull**
attribute. For this scenario use the **ValidateNotNullOrEmpty**

In the following example, the value of the **ID** parameter can't be `$null`.

```powershell
Param(
    [Parameter(Mandatory=$false)]
    [ValidateNotNull()]
    $ID
)
```

### ValidateNotNullOrEmpty validation attribute

The **ValidateNotNullOrEmpty** attribute specifies that the parameter value
can't be `$null` and can't be an empty string (`""`). PowerShell generates an
error if the parameter is used in a function call, but its value is `$null`, an
empty string (`""`), or an empty array `@()`.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [String[]]
    $UserName
)
```

### ValidateDrive validation attribute

The **ValidateDrive** attribute specifies that the parameter value must
represent the path, that's referring to allowed drives only. PowerShell
generates an error if the parameter value refers to drives other than the
allowed. Existence of the path, except for the drive itself, isn't verified.

If you use relative path, the current drive must be in the allowed drive list.

```powershell
Param(
    [ValidateDrive("C", "D", "Variable", "Function")]
    [String]$Path
)
```

### ValidateUserDrive validation attribute

The **ValidateUserDrive** attribute specifies that the parameter value must
represent the path, that is referring to `User` drive. PowerShell generates an
error if the path refers to a different drive. The validation attribute only
tests for the existence of the drive portion of the path.

If you use relative path, the current drive must be `User`.

```powershell
function Test-UserDrivePath{
    [OutputType([bool])]
    Param(
      [Parameter(Mandatory=, Position=0)][ValidateUserDrive()][String]$Path
      )
    $True
}

Test-UserDrivePath -Path C:\
```

```Output
Test-UserDrivePath: Cannot validate argument on parameter 'Path'. The path
argument drive C does not belong to the set of approved drives: User.
Supply a path argument with an approved drive.
```

```powershell
Test-UserDrivePath -Path 'User:\A_folder_that_does_not_exist'
```

```Output
Test-UserDrivePath: Cannot validate argument on parameter 'Path'. Cannot
find drive. A drive with the name 'User' does not exist.
```

You can define `User` drive in Just Enough Administration (JEA) session
configurations. For this example, we create the User: drive.

```powershell
New-PSDrive -Name 'User' -PSProvider FileSystem -Root $env:HOMEPATH
```

```Output
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
User               75.76         24.24 FileSystem    C:\Users\ExampleUser

```powershell
Test-UserDrivePath -Path 'User:\A_folder_that_does_not_exist'
```

```Output
True
```

### ValidateTrustedData validation attribute

This attribute was added in PowerShell 6.1.1.

At this time, the attribute is used internally by PowerShell itself and is not
intended for external usage.

## Dynamic parameters

Dynamic parameters are parameters of a cmdlet, function, or script that are
available only under certain conditions.

For example, several provider cmdlets have parameters that are available only
when the cmdlet is used in the provider drive, or in a particular path of the
provider drive. For example, the **Encoding** parameter is available on the
`Add-Content`, `Get-Content`, and `Set-Content` cmdlets only when it's used in
a file system drive.

You can also create a parameter that appears only when another parameter is
used in the function command or when another parameter has a certain value.

Dynamic parameters can be useful, but use them only when necessary, because
they can be difficult for users to discover. To find a dynamic parameter, the
user must be in the provider path, use the **ArgumentList** parameter of the
`Get-Command` cmdlet, or use the **Path** parameter of `Get-Help`.

To create a dynamic parameter for a function or script, use the `DynamicParam`
keyword.

The syntax is as follows:

`DynamicParam {<statement-list>}`

In the statement list, use an `If` statement to specify the conditions under
which the parameter is available in the function.

Use the `New-Object` cmdlet to create a
**System.Management.Automation.RuntimeDefinedParameter** object to represent
the parameter and specify its name.

You can use a `New-Object` command to create a
**System.Management.Automation.ParameterAttribute** object to represent
attributes of the parameter, such as **Mandatory**, **Position**, or
**ValueFromPipeline** or its parameter set.

The following example shows a sample function with standard parameters named
**Name** and **Path**, and an optional dynamic parameter named **DP1**. The
**DP1** parameter is in the `PSet1` parameter set and has a type of `Int32`.
The **DP1** parameter is available in the `Get-Sample` function only when the
value of the **Path** parameter starts with `HKLM:`, indicating that it's being
used in the `HKEY_LOCAL_MACHINE` registry drive.

```powershell
function Get-Sample {
  [CmdletBinding()]
  Param([String]$Name, [String]$Path)

  DynamicParam
  {
    if ($Path.StartsWith("HKLM:"))
    {
      $attributes = New-Object -Type `
        System.Management.Automation.ParameterAttribute
      $attributes.ParameterSetName = "PSet1"
      $attributes.Mandatory = $false
      $attributeCollection = New-Object `
        -Type System.Collections.ObjectModel.Collection[System.Attribute]
      $attributeCollection.Add($attributes)

      $dynParam1 = New-Object -Type `
        System.Management.Automation.RuntimeDefinedParameter("DP1", [Int32],
          $attributeCollection)

      $paramDictionary = New-Object `
        -Type System.Management.Automation.RuntimeDefinedParameterDictionary
      $paramDictionary.Add("DP1", $dynParam1)
      return $paramDictionary
    }
  }
}
```

For more information, see
[RuntimeDefinedParameter](/dotnet/api/system.management.automation.runtimedefinedparameter).

## Switch parameters

Switch parameters are parameters with no parameter value. They're effective
only when they're used and have only one effect.

For example, the **NoProfile** parameter of **powershell.exe** is a switch
parameter.

To create a switch parameter in a function, specify the `Switch` type in the
parameter definition.

For example:

```powershell
Param([Switch]<ParameterName>)
```

Or, you can use an another method:

```powershell
Param(
    [Parameter(Mandatory=$false)]
    [Switch]
    $<ParameterName>
)
```

Switch parameters are easy to use and are preferred over Boolean parameters,
which have a more difficult syntax.

For example, to use a switch parameter, the user types the parameter in the
command.

`-IncludeAll`

To use a Boolean parameter, the user types the parameter and a Boolean value.

`-IncludeAll:$true`

When creating switch parameters, choose the parameter name carefully. Be sure
that the parameter name communicates the effect of the parameter to the user.
Avoid ambiguous terms, such as **Filter** or **Maximum** that might imply a
value is required.

## ArgumentCompleter attribute

The **ArgumentCompleter** attribute allows you to add tab completion values to
a specific parameter. An **ArgumentCompleter** attribute must be defined for
each parameter that needs tab completion. Similar to **DynamicParameters**, the
available values are calculated at runtime when the user presses <kbd>Tab</kbd>
after the parameter name.

To add an **ArgumentCompleter** attribute, you need to define a script block
that determines the values. The script block must take the following
parameters in the order specified below. The parameter's names don't matter as
the values are provided positionally.

The syntax is as follows:

```powershell
Param(
    [Parameter(Mandatory)]
    [ArgumentCompleter({
        param ( $commandName,
                $parameterName,
                $wordToComplete,
                $commandAst,
                $fakeBoundParameters )
        # Perform calculation of tab completed values here.
    })]
)
```

### ArgumentCompleter script block

The script block parameters are set to the following values:

- `$commandName` (Position 0) - This parameter is set to the name of the
  command for which the script block is providing tab completion.
- `$parameterName` (Position 1) - This parameter is set to the parameter whose
  value requires tab completion.
- `$wordToComplete` (Position 2) - This parameter is set to value the user has
  provided before they pressed <kbd>Tab</kbd>. Your script block should use
  this value to determine tab completion values.
- `$commandAst` (Position 3) - This parameter is set to the Abstract Syntax
  Tree (AST) for the current input line. For more information, see
  [Ast Class](/dotnet/api/system.management.automation.language.ast).
- `$fakeBoundParameters` (Position 4) - This parameter is set to a hashtable
  containing the `$PSBoundParameters` for the cmdlet, before the user pressed
  <kbd>Tab</kbd>. For more information, see
  [about_Automatic_Variables](about_Automatic_Variables.md).

The **ArgumentCompleter** script block must unroll the values using the
pipeline, such as `ForEach-Object`, `Where-Object`, or another suitable method.
Returning an array of values causes PowerShell to treat the entire array as
**one** tab completion value.

The following example adds tab completion to the **Value** parameter. If only
the **Value** parameter is specified, all possible values, or arguments, for
**Value** are displayed. When the **Type** parameter is specified, the
**Value** parameter only displays the possible values for that type.

In addition, the `-like` operator ensures that if the user types the following
command and uses <kbd>Tab</kbd> completion, only **Apple** is returned.

`Test-ArgumentCompleter -Type Fruits -Value A`

```powershell
function Test-ArgumentCompleter {
[CmdletBinding()]
 param (
        [Parameter(Mandatory=$true)]
        [ValidateSet('Fruits', 'Vegetables')]
        $Type,
        [Parameter(Mandatory=$true)]
        [ArgumentCompleter( {
            param ( $commandName,
                    $parameterName,
                    $wordToComplete,
                    $commandAst,
                    $fakeBoundParameters )

            $possibleValues = @{
                Fruits = @('Apple', 'Orange', 'Banana')
                Vegetables = @('Tomato', 'Squash', 'Corn')
            }
            if ($fakeBoundParameters.ContainsKey('Type'))
            {
                $possibleValues[$fakeBoundParameters.Type] | Where-Object {
                    $_ -like "$wordToComplete*"
                }
            }
            else
            {
                $possibleValues.Values | ForEach-Object {$_}
            }
        } )]
        $Value
      )
}
```

## ArgumentCompletions attribute

The **ArgumentCompletions** attribute allows you to add tab completion values to
a specific parameter. An **ArgumentCompletions** attribute must be defined for
each parameter that needs tab completion. Similar to **ArgumentCompleter**, the
available values are calculated at runtime when the user presses <kbd>Tab</kbd>
after the parameter name.

Unlike the **ArgumentCompleter** attribute which needs a scriptblock to define
the options, the **ArgumentCompletions** attribute only needs a list of string
options to enable tab completion.

The syntax is as follows:

```powershell
function Test-ArgumentCompletions {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [ArgumentCompletions('Fruits', 'Vegetables')]
        $Type,

        [Parameter()]
        [ArgumentCompletions('Apple', 'Banana', 'Orange')]
        $Fruit,

        [Parameter()]
        [ArgumentCompletions('Tomato', 'Corn', 'Squash')]
        $Vegetable
    )
}
```

Each of the parameters is provided a list of options to the
**ArgumentCompletions** attribute to enable tab completion.

This attribute was introduced in PowerShell Core 6.0

## See also

[about_Automatic_Variables](about_Automatic_Variables.md)

[about_Functions](about_Functions.md)

[about_Functions_Advanced](about_Functions_Advanced.md)

[about_Functions_Advanced_Methods](about_Functions_Advanced_Methods.md)

[about_Functions_CmdletBindingAttribute](about_Functions_CmdletBindingAttribute.md)

[about_Functions_OutputTypeAttribute](about_Functions_OutputTypeAttribute.md)
