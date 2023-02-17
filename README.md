# Data Processing Script

Data Processing Script language

This is a script languange intended for a sequencial execution to achieve a final file with data processed by several command lines.

The execution is sequencial and have no conditional or jump. And have a simple error handler to output some informatiom.

The struct of script consists in Sections and Variables.

Sections are between brackets `[` `]`.

Variable are between `$`.

Sections and Variable names are case insensitive.

It is the same thing: `[section]`  or `[Section]`  or `[SECTION]`.

It is the same thing: `$variable$` or `$Variable$` or `$VARIABLE$`.

Some Sections need be closed and some do not.


To close a section, use its open name.

Example:
```
[SectionA]
To processes.
[/SectionA]
```

When inside a sections have a value of open or closing other section, it will be ignored even it is a valid section name.
```
[SectionA]
To processes.
[SectionB]  Ignored as section, will be processed as text
To processes.
[/SectionB] Ignored as section, will be processed as text
To processes.
[/SectionA]
```

In sections is expected not have a value of is close.
Example:
```
[SectionA]
This is a text inside SectionA and its close is [/SectionA] <--- It is not expected.
[/SectionA]
```

It will cause unexpected behaviors between the two close section.
An error will be trigged and execution stoped.

### Sections:

| Section       | Type  | Description                      |
| ------------- | ----- | -------------------------------- |
| `[COMMENT]`   | Close | Comment                          |
| `[CR]`        |       | Carrige Return                   |
| `[LF]`        |       | Line Feed                        |
| `[CRLF]`      |       | Carrige Return and Line Feed     |
| `[RUNLEVELS]` |       | Define label for each Run Levels |
| `[RUNLEVEL]`  |       | Run Level                        |
| `[ERROR]`     | Close | Error handler                    |
| `[CMD]`       |       | Set commands                     |
| `[VAR]`       | Close | Set variable                     |
| `[RUN]`       |       | Run command                      |
| `[PRINT]`     | Close | Print on screen                  |
| `[WRITE]`     | Close | Write to file                    |
| `[READ]`      |       | Read from file                   |
| `[CHD]`       |       | Change directory                 |

### System Variables:

| Variable     | Description               |
| ------------ | ------------------------- |
| `$RUNLEVEL$` | Run Level number          |
| `$CMD$`      | Object of commands        |
| `$CWD$`      | Current work directory    |
| `$ERROR$`    | Object with error details |

## Sections:

### `[COMMENT]`

It is a section that will not be processed and ignored on exection.

### `[CR]` `[LF]` `[CRLF]`

Set the new line character to be used on `[VAR]`, `[WRITE]` and `[PRINT]`.

| Section  | Meaning                            |
| -------- | ---------------------------------- |
| `[CR]`   | Carrige Return: \r, x0A, char(10). |
| `[LF]`   | Line Feed     : \n, x0C, char(13). |
| `[CRLF]` | Carrige Return and Line Feed.      |

### `[RUNLEVELS]`

Label each Run Level.

Example:
```
[RUNLEVELS]
DebugLevel1
DebugLevel2
DebugLevel3

[RUNLEVEL]
DebugLevel1 DebugLevel3
```

*** TODO: *Implement level names. Not implemented yet.*

### `[RUNLEVEL]`

Value that can be used on Closed Sections to use/ignore specific lines.

The value is numeric and the bitwise comparison will filter the line to be used.

Run Level 0 is the default value and it will not use all lines flaged, except with 0.

Example:
```
[RUNLEVEL]
1
```

You can not se the Run Level with a variable value.

See more on Closed Sections (where it is used).

*** TODO: *With the `[RUNLEVELS]` it will not use bitwise comparison.*

### `[ERROR]`

What will be printed when an error is trigged.

This is the only section the system variable \$ERROR\$ is be available. See more on Variables \$ERROR\$.

Example:
```
[ERROR]
Error level: $Error.ErrorLevel$.
Error on file $Error.Filename$ in line $Error.Line$.
$Error.Description$
Section:
$Error.Section$
[/ERROR]
```

See more on Closed Sections.

### `[CMD]`

Is a list of command to be used on `[RUN]` section.

This is the unique dynamic generated object.

The line starts with an alias of command folowed by an equal sign `=` and then the command line.

The alias with me trimmed and the command with be trimmed only at begin.

Alias can have only alphanumeric values, underscore `_` and are case insensitive.

Example:
```
[CMD]
Command_1 = filename.exe
Command_2 = filename.exe /Param
Command_3 = filename.exe /ParamEx
```

See mor on Variables \$CMD\$

### `[VAR]`

Set a variable.

Variables can have only alphanumeric values, underscore `_` and are case insensitive.

Example:
```
[VAR variable_1]
Value 1
[/VAR]
```

See more on Closed Sections.

### `[RUN]`

Run one line of a command line.

Variables with multiple lines will be joined with a space replacing the new line character.

Example:
```
[RUN output_from_run]
cmd.exe /C echo Printing some value.

[PRINT]
$output_from_run$
[/PRINT]
```

Like in a Closed Section, you can use the backtick `` ` `` to continue the command line on the next line.

But it is feature only works on literal command, not inside variables.

See more on Closed Sections backtick.

### `[PRINT]`

Print on screen.

It does not have an output value.

Example:
```
[PRINT]
Hello world!
[/PRINT]

[VAR foo]
Value of Foo.
[/VAR]

[PRINT]
Foo's value is: $foo$
[/PRINT]
```

See more on Closed Sections.

### `[WRITE]`

Write to file.

Example:
```
[WRITE filename.txt]
To file.
[/WRITE]

[VAR filename]
filename.txt
[/VAR]

[WRITE $filename$]
To file.
[/WRITE]
```

*** TODO: *Escape the close bracket when it is part of the literal filename. On variable it is not a issue.*

See more on Closed Sections.

### `[READ]`

Read file.

It have two obligatory parameters: the file and the output variable.

Example:
```
[READ filename.txt => output_variable]

[PRINT]
$output_variable$
[/PRINT]
```

*** TODO: *Escape the close bracket when it is part of the literal filename. On variable it is not a issue.*

See more on Closed Sections parameters.

### `[CHD]`

Change current directory.

Example:
```
[CHD C:\destiny\path\]

[CHD C:\path to change\]

[CHD $variable$]
```

*** TODO: *Escape the close bracket when it is part of the literal path. On variable it is not a issue.*

### Closed Sections:

Closed Sections can have parameters. It consists in varaibles and an output.

It possible to define what variables will be procced on section and indicate the output of the section.

Output can be a variable or a file.

The list of variable to be processed are separate with space and are optional. When have, to define the output, use an "arrow" `=>`.

If not informed variables, all will be processed.

The output, if not have a variable to be processed, you can optionaly use an "arrow" `=>`.

Example:
```
[VAR v1]
(This is v1)
[/VAR]

[VAR => v2]
(This is v2)
[/VAR]

[VAR v2 => v3]
{This is v3 with v2 inside -> $v2$}
[/VAR]

[VAR v1 v3 => v4]
Process     v1 $v1$.
Not process v2 $v2$.
Process     v3 $v3$.
And output to $v4$ (and that $v4$ will not be processed)
[/VAR]

[WRITE output.txt]
Output to a file.
[/WRITE]

[WRITE => output.txt]
Output to a file.
[/WRITE]

[WRITE v1 v2 => output.txt]
Output to a file.
With v1 and v2:
$v1$
$v2$
[/WRITE]
```

`[VAR]`, `[RUN]` and `[READ]` will output to a variable. *`[RUN]` and `[READ]` are not Closed Section.

`[WRITE]` will output to a file.

`[ERROR]` and `[PRINT]` does not have output, it always variables to be processed.

On a section on the process area if you put a number on begin of the line and it have a tab character, it repesent the flag.

Example:
```
[VAR foo]
This line will not be ignored.
0	This line will not be ignored too.
1	This line will only be processed when Run Level have the one of the bits 0001.
2	This line will only be processed when Run Level have the one of the bits 0010.
3	This line will only be processed when Run Level have the one of the bits 0011.
4	This line will only be processed when Run Level have the one of the bits 0100.
5	This line will only be processed when Run Level have the one of the bits 0101.
6	This line will only be processed when Run Level have the one of the bits 0110.
7	This line will only be processed when Run Level have the one of the bits 0111.
8	This line will only be processed when Run Level have the one of the bits 1000.
[/VAR]
```

You can use one or more tabs after the flag number and it will be ignored.

If you need the tab character, after the ignored tabs, add a pipe | character and the tabs you need. The pipe will be ignored too.

And if you need the line start with a pipe, add a pipe after the pipe.

Example:
```
[PRINT]
1	|	Start with one tab.
2		|		Start with two tabs.
3	|		Start with two tabs.
4		|	Start with one tab.
5	|| Start with a pipe.
[/PRINT]
```

To ignore the line wrap, add as last character of the line a backtick `` ` ``.

If have anothe character after that, the next line will be a another line.

Example:
```
[WRITE filename.txt]
First line begin. `
First line continue. `
1	Continue only when Run Level 1. `
2	Continue only when Run Level 2. `
And this is the finish of first line.
[/WRITE]
```

Obs: Two backtick at the end of the line with be interpreted as the before last a literal one and the last indicate a next line.

The value of a Closed Section begins on the first line after the open section.
And the end is the line above the close section.

Example:
```
[PRINT]
One line without new line character.
[/PRINT]


[WRITE filename.txt]

One line with a new line character at the begin.
[/WRITE]


[VAR var1]
One line with a new line character at the end.

[/VAR]


[VAR var2]

One line with one new line character at the begin and one at the end.

[/VAR]
```

## Variables:

### `$RUNLEVEL$`

Run Level value of the script.

### `$CMD$`

System object with commands to be used on `[RUN]` section.

Example:
```
[CMD]
Command_1 = filename.exe
Command_2 = filename.exe /Param
Command_3 = filename.exe /ParamEx

[RUN output_1]
$CMD.Command_1$

[RUN output_2]
$CMD.Command_2$ -f file1.txt

[RUN output_3]
$CMD.Command_3$ /xyz
```

### `$CWD$`

Current work directory

### `$ERROR$`

This variable is only availabel on [ERROR] section.

| Poperty               | Description                                        |
| --------------------- | -------------------------------------------------- |
| `$ERROR.ErrorLevel$`  |  Error level when returned from a `[RUN]` section. |
| `$ERROR.Filename$`    |                                                    |
| `$ERROR.Line$`        |                                                    |
| `$ERROR.Description$` |                                                    |
| `$ERROR.Desc$`        |                                                    |
| `$ERROR.Section$`     |  All the section where the error occur.            |
