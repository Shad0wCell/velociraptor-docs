---
title: Windows.System.CmdShell
hidden: true
tags: [Client Artifact]
---

This artifact allows running arbitrary commands through the system
shell cmd.exe.

Since Velociraptor typically runs as system, the commands will also
run as System.

This is a very powerful artifact since it allows for arbitrary
command execution on the endpoints. Therefore this artifact requires
elevated permissions (specifically the `EXECVE`
permission). Typically it is only available with the `administrator`
role.

Note there are some limitations with passing commands to the cmd.exe
shell, such as when specifying quoted paths or command-line
arguments with special characters. Using Windows.System.PowerShell
artifact is likely a better option in these cases.


```yaml
name: Windows.System.CmdShell
description: |
  This artifact allows running arbitrary commands through the system
  shell cmd.exe.

  Since Velociraptor typically runs as system, the commands will also
  run as System.

  This is a very powerful artifact since it allows for arbitrary
  command execution on the endpoints. Therefore this artifact requires
  elevated permissions (specifically the `EXECVE`
  permission). Typically it is only available with the `administrator`
  role.

  Note there are some limitations with passing commands to the cmd.exe
  shell, such as when specifying quoted paths or command-line
  arguments with special characters. Using Windows.System.PowerShell
  artifact is likely a better option in these cases.

required_permissions:
  - EXECVE

precondition:
  SELECT OS From info() where OS = 'windows'

parameters:
  - name: Command
    default: "dir C:\\"

sources:
  - query: |
      SELECT * FROM execve(argv=["cmd.exe", "/c", Command])

```
