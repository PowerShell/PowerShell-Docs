---
description: Describes a language command you can use to run statements based on a conditional test. 
keywords: powershell,cmdlet
Locale: en-US
ms.date: 3/4/2019
online version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_for?view=powershell-7&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about For
---
# about_For

## Short description
Describes a language command you can use to run statements based on a
conditional test.

## Long description

The `For` statement (also known as a `For` loop) is a language construct you
can use to create a loop that runs commands in a command block while a
specified condition evaluates to `$true`.

A typical use of the `For` loop is to iterate an array of values and to
operate on a subset of these values. In most cases, if you want to iterate
all the values in an array, consider using a `Foreach` statement.

### Syntax

The following shows the `For` statement syntax.

```
for (<Init>; <Condition>; <Repeat>)
{
    <Statement list>
}
```

The **Init** placeholder represents one or more commands that are run before
the loop begins. You typically use the **Init** portion of the statement to
create and initialize a variable with a starting value.

This variable will then be the basis for the condition to be tested in the
next portion of the `For` statement.

The **Condition** placeholder represents the portion of the `For` statement
that resolves to a `$true` or `$false` **Boolean** value. PowerShell evaluates
the condition each time the `For` loop runs. If the statement is `$true`, the
commands in the command block run, and the statement is evaluated again. If the
condition is still `$true`, the commands in the **Statement list** run again. The loop
is repeated until the condition becomes `$false`.

The **Repeat** placeholder represents one or more commands, separated
by commas, that are executed each time the loop repeats. Typically, this is
used to modify a variable that is tested inside the **Condition** part
of the statement.

The **Statement list** placeholder represents a set of one or more
commands that are run each time the loop is entered or repeated. The
contents of the **Statement list** are surrounded by braces.

### Support for multiple operations

The following syntaxes are supported for multiple assignment operations in the
**Init** statement:

```powershell
# Comma separated assignment expressions enclosed in parenthesis.
for (($i = 0), ($j = 0); $i -lt 10; $i++)
{
    "`$i:$i"
    "`$j:$j"
}

# Sub-expression using the semicolon to separate statements.
for ($($i = 0;$j = 0); $i -lt 10; $i++)
{
    "`$i:$i"
    "`$j:$j"
}
```

The following syntaxes are supported for multiple assignment operations in the
**Repeat** statement:

```powershell
# Comma separated assignment expressions enclosed in parenthesis.
for (($i = 0), ($j = 0); $i -lt 10; ($i++), ($j++))
{
    "`$i:$i"
    "`$j:$j"
}

# Sub-expression using the semicolon to separate statements.
for ($($i = 0;$j = 0); $i -lt 10; $($i++;$j++))
{
    "`$i:$i"
    "`$j:$j"
}
```

> [!NOTE]
> Operations other than pre or post increment may not work with all syntaxes.

For multiple **Conditions** use logical operators as demonstrated by the
following example.

```powershell
for (($i = 0), ($j = 0); $i -lt 10 -and $j -lt 10; $i++,$j++)
{
    "`$i:$i"
    "`$j:$j"
}
```

For more information, see [about_Logical_Operators](about_Logical_Operators.md).

### Examples

At a minimum, a `For` statement requires the parenthesis surrounding the
**Init**, **Condition**, and **Repeat** part of the statement
and a command surrounded by braces in the **Statement list** part of
the statement.

Note that the upcoming examples intentionally show code outside the `For`
statement. In later examples, code is integrated into the `For` statement.

For example, the following `For` statement continually displays the value of
the `$i` variable until you manually break out of the command by pressing
CTRL+C.

```powershell
$i = 1
for (;;)
{
    Write-Host $i
}
```

You can add additional commands to the statement list so that the value of
`$i` is incremented by 1 each time the loop is run, as the following example
shows.

```powershell
for (;;)
{
    $i++; Write-Host $i
}
```

Until you break out of the command by pressing CTRL+C, this statement will
continually display the value of the `$i` variable as it is incremented by 1
each time the loop is run.

Rather than change the value of the variable in the statement list part of
the `For` statement, you can use the **Repeat** portion of the `For`
statement instead, as follows.

```powershell
$i=1
for (;;$i++)
{
    Write-Host $i
}
```

This statement will still repeat indefinitely until you break out of the
command by pressing CTRL+C.

You can terminate the `For` loop using a *condition*. You can place a condition
using the **Condition** portion of the `For` statement. The `For`
loop terminates when the condition evaluates to `$false`.

In the following example, the `For` loop runs while the value of `$i` is less
than or equal to 10.

```powershell
$i=1
for(;$i -le 10;$i++)
{
    Write-Host $i
}
```

Instead of creating and initializing the variable outside the `For`
statement, you can perform this task inside the `For` loop by using the
**Init** portion of the `For` statement.

```powershell
for($i=1; $i -le 10; $i++){Write-Host $i}
```

You can use carriage returns instead of semicolons to delimit the
**Init**, **Condition**, and **Repeat** portions of the `For`
statement. The following example shows a `For` that uses this alternative
syntax.

```powershell
for ($i = 0
  $i -lt 10
  $i++){
  $i
}
```

This alternative form of the `For` statement works in PowerShell script files
and at the PowerShell command prompt. However, it is easier to use
the `For` statement syntax with semicolons when you enter interactive
commands at the command prompt.

The `For` loop is more flexible than the `Foreach` loop because it allows you
to increment values in an array or collection by using patterns. In the
following example, the `$i` variable is incremented by 2 in the
**Repeat** portion of the `For` statement.

```powershell
for ($i = 0; $i -le 20; $i += 2)
{
    Write-Host $i
}
```

The `For` loop can also be written on one line as in the following example.

```powershell
for ($i = 0; $i -lt 10; $i++) { Write-Host $i }
```

## SEE ALSO

[about_Comparison_Operators](about_Comparison_Operators.md)

[about_Foreach](about_Foreach.md)
