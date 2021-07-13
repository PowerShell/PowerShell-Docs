---
description: Describes the operators that compare values in PowerShell.
Locale: en-US
ms.date: 07/06/2021
online version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_comparison_operators?view=powershell-7.2&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about Comparison Operators
---
# about_Comparison_Operators

## Short description

The comparison operators in PowerShell can either compare two values or filter
elements of a collection against an input value.

## Long description

Comparison operators let you compare values or finding values that match
specified patterns. PowerShell includes the following comparison operators:

|    Type     |   Operator   |              Comparison test              |
| ----------- | ------------ | ----------------------------------------- |
| Equality    | -eq          | equals                                    |
|             | -ne          | not equals                                |
|             | -gt          | greater than                              |
|             | -ge          | greater than or equal                     |
|             | -lt          | less than                                 |
|             | -le          | less than or equal                        |
| Matching    | -like        | string matches wildcard pattern           |
|             | -notlike     | string does not match wildcard pattern    |
|             | -match       | string matches regex pattern              |
|             | -notmatch    | string does not match regex pattern       |
| Replacement | -replace     | replaces strings matching a regex pattern |
| Containment | -contains    | collection contains a value               |
|             | -notcontains | collection does not contain a value       |
|             | -in          | value is in a collection                  |
|             | -notin       | value is not in a collection              |
| Type        | -is          | both objects are the same type            |
|             | -isnot       | the objects are not the same type         |

## Common features

By default, all comparison operators are case-insensitive. To make a comparison
operator case-sensitive, add a `c` after the `-`. For example, `-ceq` is the
case-sensitive version of `-eq`. To make the case-insensitivity explicit,
add an `i` after `-`. For example, `-ieq` is the explicitly case-insensitive
version of `-eq`.

When the input of an operator is a scalar value, the operator returns a
**Boolean** value. When the input is a collection, the operator returns the
elements of the collection that match the right-hand value of the expression.
If there are no matches in the collection, comparison operators return an empty
array. For example:

```powershell
$a = (1, 2 -eq 3)
$a.GetType().Name
$a.Count
```

```output
Object[]
0
```

There are a few exceptions:

- The containment and type operators always return a **Boolean** value
- The `-replace` operator returns the replacement result
- The `-match` and `-notmatch` operators also populate the `$Matches` automatic
  variable unless the left-hand side of the expression is a collection.

## Equality operators

### -eq and -ne

When the left-hand side is scalar, `-eq` returns **True** if the right-hand
side is an exact match, otherwise, `-eq` returns **False**. `-ne` does the
opposite; it returns **False** when both sides match; otherwise, `-ne` returns
**True**.

Example:

```powershell
2 -eq 2                 # Output: True
2 -eq 3                 # Output: False
"abc" -eq "abc"         # Output: True
"abc" -eq "abc", "def"  # Output: False
"abc" -ne "def"         # Output: True
"abc" -ne "abc"         # Output: False
"abc" -ne "abc", "def"  # Output: True
```

When the left-hand side is a collection, `-eq` returns those members that match
the right-hand side, while `-ne` filters them out.

Example:

```powershell
1,2,3 -eq 2             # Output: 2
"abc", "def" -eq "abc"  # Output: abc
"abc", "def" -ne "abc"  # Output: def
```

These operators process all elements of the collection. Example:

```powershell
"zzz", "def", "zzz" -eq "zzz"
```

```output
zzz
zzz
```

The equality operators accept any two objects, not just a scalar or collection.
But the comparison result is not guaranteed to be meaningful for the end-user.
The following example demonstrates the issue.

```powershell
class MyFileInfoSet {
    [String]$File
    [Int64]$Size
}
$a = [MyFileInfoSet]@{File = "C:\Windows\explorer.exe"; Size = 4651032}
$b = [MyFileInfoSet]@{File = "C:\Windows\explorer.exe"; Size = 4651032}
$a -eq $b
```

```Output
False
```

In this example, we created two objects with identical properties. Yet, the
equality test result is **False** because they are different objects. To create
comparable classes, you need to implement [System.IEquatable\<T>][2] in your
class. The following example demonstrates the partial implementation of a
**MyFileInfoSet** class that implements [System.IEquatable\<T>][2] and has two
properties, **File** and **Size**. The `Equals()` method returns **True** if the
File and Size properties of two **MyFileInfoSet** objects are the same.

```powershell
class MyFileInfoSet : System.IEquatable[Object] {
    [String]$File
    [Int64]$Size

    [bool] Equals([Object] $obj) {
        return ($this.File -eq $obj.File) -and ($this.Size -eq $obj.Size)
    }
}
$a = [MyFileInfoSet]@{File = "C:\Windows\explorer.exe"; Size = 4651032}
$b = [MyFileInfoSet]@{File = "C:\Windows\explorer.exe"; Size = 4651032}
$a -eq $b
```

```Output
True
```

A prominent example of comparing arbitrary objects is to find out if they are
null. But if you need to determine whether a variable is `$null`, you must put
`$null` on the left-hand side of the equality operator. Putting it on the
right-hand side does not do what you expect.

For example, let `$a` be an array containing null elements:

```powershell
$a = 1, 2, $null, 4, $null, 6
```

The following tests that `$a` is not null.

```powershell
$null -ne $a
```

```output
True
```

The following, however, filers out all null elements from `$a`:

```powershell
$a -ne $null # Output: 1, 2, 4, 6
```

```output
1
2
4
6
```

### -gt, -ge, -lt, and -le

`-gt`, `-ge`, `-lt`, and `-le` behave very similarly. When both sides are scalar
they return **True** or **False** depending on how the two sides compare:

| Operator | Returns True when...                   |
| -------- | -------------------------------------- |
| -gt      | The left-hand side is greater          |
| -ge      | The left-hand side is greater or equal |
| -lt      | The left-hand side is smaller          |
| -le      | The left-hand side is smaller or equal |

In the following examples, all statements return **True**.

```powershell
8 -gt 6  # Output: True
8 -ge 8  # Output: True
6 -lt 8  # Output: True
8 -le 8  # Output: True
```

> [!NOTE]
> In most programming languages the greater-than operator is `>`. In
> PowerShell, this character is used for redirection. For details, see
> [about_Redirection][3].

When the left-hand side is a collection, these operators compare each member of
the collection with the right-hand side. Depending on their logic, they either
keep or discard the member.

Example:

```powershell
$a=5, 6, 7, 8, 9

Write-Output "Test collection:"
$a

Write-Output "`nMembers greater than 7"
$a -gt 7

Write-Output "`nMembers greater than or equal to 7"
$a -ge 7

Write-Output "`nMembers smaller than 7"
$a -lt 7

Write-Output "`nMembers smaller than or equal to 7"
$a -le 7
```

```output
Test collection:
5
6
7
8
9

Members greater than 7
8
9

Members greater than or equal to 7
7
8
9

Members smaller than 7
5
6

Members smaller than or equal to 7
5
6
7
```

These operators work with any class that implements [System.IComparable][1].

Examples:

```powershell
# Date comparison
[DateTime]'2001-11-12' -lt [DateTime]'2020-08-01' # True

# Sorting order comparison
'a' -lt 'z'           # True; 'a' comes before 'z'
'macOS' -ilt 'MacOS'  # False
'MacOS' -ilt 'macOS'  # False
'macOS' -clt 'MacOS'  # True; 'm' comes before 'M'
```

The following example demonstrates that there is no symbol on an American QWERTY
keyboard that gets sorted after 'a'. It feeds a set containing all such symbols
to the `-gt` operator to compare them against 'a'. The output is an empty array.

```powershell
$a=' ','`','~','!','@','#','$','%','^','&','*','(',')','_','+','-','=',
   '{','}','[',']',':',';','"','''','\','|','/','?','.','>',',','<'
$a -gt 'a'
# Output: Nothing
```

If the two sides of the operators are not reasonably comparable, these operators
raise a non-terminating error.

## Matching operators

The matching operators (`-like`, `-notlike`, `-match`, and `-notmatch`) find
elements that match or do not match a specified pattern. The pattern for `-like`
and `-notlike` is a wildcard expression (containing `*`, `?`, and `[ ]`), while
`-match` and `-notmatch` accept a regular expression (Regex).

The syntax is:

```
<string[]> -like    <wildcard-expression>
<string[]> -notlike <wildcard-expression>
<string[]> -match    <regular-expression>
<string[]> -notmatch <regular-expression>
```

When the input of these operators is a scalar value, they return a **Boolean**
value. When the input is a collection of values, the operators return any
matching members. If there are no matches in a collection, the operators return
an empty array.

### -like and -notlike

`-like` and `-notlike` behave similarly to `-eq` and `-ne`, but the right-hand
side could be a string containing [wildcards](about_Wildcards.md).

Example:

```powershell
"PowerShell" -like    "*shell"           # Output: True
"PowerShell" -notlike "*shell"           # Output: False
"PowerShell" -like    "Power?hell"       # Output: True
"PowerShell" -notlike "Power?hell"       # Output: False
"PowerShell" -like    "Power[p-w]hell"   # Output: True
"PowerShell" -notlike "Power[p-w]hell"   # Output: False

"PowerShell", "Server" -like "*shell"    # Output: PowerShell
"PowerShell", "Server" -notlike "*shell" # Output: Server
```

### -match and -notmatch

`-match` and `-notmatch` use regular expressions to search for pattern in the
left-hand side values. Regular expressions can match complex patterns like
email addresses, UNC paths, or formatted phone numbers. The right-hand side
string must adhere to the [regular expressions](about_Regular_Expressions.md)
rules.

Scalar examples:

```powershell
# Partial match test, showing how differently -match and -like behave
"PowerShell" -match 'shell'        # Output: True
"PowerShell" -like  'shell'        # Output: False

# Regex syntax test
"PowerShell" -match    '^Power\w+' # Output: True
'bag'        -notmatch 'b[iou]g'   # Output: True
```

If the input is a collection, the operators return the matching members of that
collection.

Collection examples:

```powershell
"PowerShell", "Super PowerShell", "Power's hell" -match '^Power\w+'
# Output: PowerShell

"Rhell", "Chell", "Mel", "Smell", "Shell" -match "hell"
# Output: Rhell, Chell, Shell

"Bag", "Beg", "Big", "Bog", "Bug"  -match 'b[iou]g'
#Output: Big, Bog, Bug

"Bag", "Beg", "Big", "Bog", "Bug"  -notmatch 'b[iou]g'
#Output: Bag, Beg
```

`-match` and `-notmatch` support regex capture groups. Each time they run on
scalar input, and the `-match` result is **True**, or the `-notmatch` result is
**False**, they overwrite the `$Matches` automatic variable. `$Matches` is a
**Hashtable** that always has a key named '0', which stores the entire match.
If the regular expression contains capture groups, the `$Matches` contains
additional keys for each group.

Example:

```powershell
$string = 'The last logged on user was CONTOSO\jsmith'
$string -match 'was (?<domain>.+)\\(?<user>.+)'

$Matches

Write-Output "`nDomain name:"
$Matches.domain

Write-Output "`nUser name:"
$Matches.user
```

```output
True

Name                           Value
----                           -----
domain                         CONTOSO
user                           jsmith
0                              was CONTOSO\jsmith

Domain name:
CONTOSO

User name:
jsmith
```

When the `-match` result is **False**, or the `-notmatch` result is **True**,
or when the input is a collection, the `$Matches` automatic variable is not
overwritten. Consequently, it will contain the previously set value, or `$null`
if the variable has not been set. When referencing `$Matches` after invoking
one of these operators, consider verifying that the variable was set by the
current operator invocation by using a condition statement.

Example:

```powershell
if ("<version>1.0.0</version>" -match '<version>(.*?)</version>') {
    $Matches
}
```

For details, see [about_Regular_Expressions](about_Regular_Expressions.md) and
[about_Automatic_Variables](about_Automatic_Variables.md).

## Replacement operator

### Replacement with regular expressions

Like `-match`, the `-replace` operator uses regular expressions to find the
specified pattern. But unlike `-match`, it replaces the matches with another
specified value.

Syntax:

```
<input> -replace <regular-expression>, <substitute>
```

The operator replaces all or part of a value with the specified value using
regular expressions. You can use the operator for many administrative tasks,
such as renaming files. For example, the following command changes the file
name extensions of all `.txt` files to `.log`:

```powershell
Get-ChildItem *.txt | Rename-Item -NewName { $_.name -replace '\.txt$','.log' }
```

By default, the `-replace` operator is case-insensitive. To make it case
sensitive, use `-creplace`. To make it explicitly case-insensitive, use
`-ireplace`.

Examples:

```powershell
"book" -ireplace "B", "C" # Case insensitive
"book" -creplace "B", "C" # Case-sensitive; hence, nothing to replace
```

```Output
Cook
book
```

### Regular expressions substitutions

It is also possible to use regular expressions to dynamically replace text
using capturing groups, and substitutions. Capture groups can be referenced in
the `<substitute>` string using the dollar sign (`$`) character before the
group identifier.

In the following example, the `-replace` operator accepts a username in the form
of `DomainName\Username` and converts to the `Username@DomainName` format:

```powershell
$SearchExp = '^(?<DomainName>[\w-.]+)\\(?<Username>[\w-.]+)$'
$ReplaceExp = '${Username}@${DomainName}'

'Contoso.local\John.Doe' -replace $SearchExp,$ReplaceExp
```

```output
John.Doe@Contoso.local
```

> [!WARNING]
> The `$` character has syntatic roles in both PowerShell and regular
> expressions:
>
> - In PowerShell, between double quotation marks, it designates variables and
>   acts as a subexpression operator.
> - In Regex search strings, it denotes end of the line
> - In Regex substitution strings, it denotes captured groups.Be sure
>   to either put your regular expressions between single quotation marks or
>   insert a backtick (`` ` ``) character before them.

For example:

```powershell
$1 = 'Goodbye'

'Hello World' -replace '(\w+) \w+', "$1 Universe"
# Output: Goodbye Universe

'Hello World' -replace '(\w+) \w+', '$1 Universe'
# Output: Hello Universe
```

`$$` in Regex denotes a literal `$`. This `$$` in the substitution string to
include a literal `$` in the resulting replacement. For example:

```powershell
'5.72' -replace '(.+)', '$ $1' # Output: $ 5.72
'5.72' -replace '(.+)', '$$$1' # Output: $5.72
'5.72' -replace '(.+)', '$$1'  # Output: $1
```

To learn more, see [about_Regular_Expressions](about_Regular_Expressions.md) and
[Substitutions in Regular Expressions][4].

### Substituting in a collection

When the `<input>` to the `-replace` operator is a collection, PowerShell
applies the replacement to every value in the collection. For example:

```powershell
"B1","B2","B3","B4","B5" -replace "B", 'a'
a1
a2
a3
a4
a5
```

### Replacement with a script block

In PowerShell 6 and later, the `-replace` operator also accepts a script block
that performs the replacement. The script block runs once for every match.

Syntax:

```powershell
<String> -replace <regular-expression>, {<Script-block>}
```

Within the script block, use the `$_` automatic variable to access the input
text being replaced and other useful information. This variable's class type is
[System.Text.RegularExpressions.Match][2].

The following example replaces each sequence of three digits with the character
equivalents. The script block runs for each set of three digits that needs to be
replaced.

```powershell
"072101108108111" -replace "\d{3}", {return [char][int]$_.Value}
```

```output
Hello
```

## Containment operators

The containment operators (`-contains`, `-notcontains`, `-in`, and `-notin`) are
similar to the equality operators, except that they always return a **Boolean**
value, even when the input is a collection. These operators stop comparing as
soon as they detect the first match, whereas the equality operators evaluate all
input members. In a very large collection, these operators return quicker than
the equality operators.

Syntax:

```
<Collection> -contains <Test-object>
<Collection> -notcontains <Test-object>
<Test-object> -in <Collection>
<Test-object> -notin <Collection>
```

### -contains and -notcontains

These operators tell whether a set includes a certain element. `-contains`
returns **True** when the right-hand side (test object) matches one of the elements
in the set. `-notcontains` returns False instead. When the test object is a
collection, these operators use reference equality, i.e. they check whether one
of the set's elements is the same instance of the test object.

Examples:

```powershell
"abc", "def" -contains "def"                  # Output: True
"abc", "def" -notcontains "def"               # Output: False
"Windows", "PowerShell" -contains "Shell"     # Output: False
"Windows", "PowerShell" -notcontains "Shell"  # Output: True
"abc", "def", "ghi" -contains "abc", "def"    # Output: False
"abc", "def", "ghi" -notcontains "abc", "def" # Output: True
```

More complex examples:

```powershell
$DomainServers = "ContosoDC1","ContosoDC2","ContosoFileServer","ContosoDNS",
                 "ContosoDHCP","ContosoWSUS"
$thisComputer  = "ContosoDC2"

$DomainServers -contains $thisComputer
# Output: True

$a = "abc", "def"
"abc", "def", "ghi" -contains $a # Output: False
$a, "ghi" -contains $a           # Output: True
```

### -in and -notin

The `-in` and `-notin` operators were introduced in PowerShell 3 as the
syntactic reverse of the of `-contains` and `-notcontains` operators. `-in`
returns **True** when the left-hand side `<test-object>` matches one of the
elements in the set. `-notin` returns **False** instead. When the test object
is a set, these operators use reference equality to check whether one of the
set's elements is the same instance of the test object.

The following examples do the same thing that the examples for `-contains`
and `-notcontains` do, but they are written with `-in` and `-notin` instead.

```powershell
"def" -in "abc", "def"                  # Output: True
"def" -notin "abc", "def"               # Output: False
"Shell" -in "Windows", "PowerShell"     # Output: False
"Shell" -notin "Windows", "PowerShell"  # Output: True
"abc", "def" -in "abc", "def", "ghi"    # Output: False
"abc", "def" -notin "abc", "def", "ghi" # Output: True
```

More complex examples:

```powershell
$DomainServers = "ContosoDC1","ContosoDC2","ContosoFileServer","ContosoDNS",
                 "ContosoDHCP","ContosoWSUS"
$thisComputer  = "ContosoDC2"

$thisComputer -in $DomainServers
# Output: True

$a = "abc", "def"
$a -in "abc", "def", "ghi" # Output: False
$a -in $a, "ghi"           # Output: True
```

## Type comparison

The type comparison operators (`-is` and `-isnot`) are used to determine if an
object is a specific type.

Syntax:

```powershell
<object> -is <type-reference>
<object> -isnot <type-reference>
```

Example:

```powershell
$a = 1
$b = "1"
$a -is [int]           # Output: True
$a -is $b.GetType()    # Output: False
$b -isnot [int]        # Output: True
$a -isnot $b.GetType() # Output: True
```

## SEE ALSO

- [about_Operators](about_Operators.md)
- [about_Regular_Expressions](about_Regular_Expressions.md)
- [about_Wildcards](about_Wildcards.md)
- [Compare-Object](xref:Microsoft.PowerShell.Utility.Compare-Object)
- [Foreach-Object](xref:Microsoft.PowerShell.Core.ForEach-Object)
- [Where-Object](xref:Microsoft.PowerShell.Core.Where-Object)
- [about_Booleans](about_Booleans.md)

[1]: /dotnet/api/system.icomparable
[2]: /dotnet/api/system.iequatable-1
[3]: /dotnet/api/system.text.regularexpressions.match
[4]: about_Redirection.md#potential-confusion-with-comparison-operators
[5]: /dotnet/standard/base-types/substitutions-in-regular-expressions
