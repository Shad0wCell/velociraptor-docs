---
title: Windows.Sys.Users
hidden: true
tags: [Client Artifact]
---

List User accounts by inspecting registry keys. This method is a
reliable indicator for users who have physically logged into the
system and thereby created local profiles.

This will not include domain users or the output from `NetUserEnum`
- you should collect the `Windows.Sys.AllUsers` artifact to get all
possible users on the system.


```yaml
name: Windows.Sys.Users
description: |
  List User accounts by inspecting registry keys. This method is a
  reliable indicator for users who have physically logged into the
  system and thereby created local profiles.

  This will not include domain users or the output from `NetUserEnum`
  - you should collect the `Windows.Sys.AllUsers` artifact to get all
  possible users on the system.

parameters:
  - name: remoteRegKey
    default: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\*

sources:
  - precondition:
      SELECT OS From info() where OS = 'windows'

    query: |
        LET GetTimestamp(High, Low) = if(condition=High,
                then=timestamp(winfiletime=High * 4294967296 + Low))

        -- lookupSID() may not be available on deaddisk analysis
        SELECT split(string=Key.OSPath.Basename, sep="-")[-1] as Uid,
           "" AS Gid,
           lookupSID(sid=Key.OSPath.Basename) || "" AS Name,
           Key.OSPath as Description,
           ProfileImagePath as Directory,
           Key.OSPath.Basename as UUID,
           Key.Mtime as Mtime,
           {
                SELECT Mtime
                FROM stat(filename=expand(path=ProfileImagePath))
            } AS HomedirMtime,
           dict(ProfileLoadTime=GetTimestamp(
                   High=LocalProfileLoadTimeHigh, Low=LocalProfileLoadTimeLow),
                ProfileUnloadTime=GetTimestamp(
                   High=LocalProfileUnloadTimeHigh, Low=LocalProfileUnloadTimeLow)
           ) AS Data
        FROM read_reg_key(globs=remoteRegKey, accessor="registry")

```
