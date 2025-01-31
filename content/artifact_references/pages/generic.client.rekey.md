---
title: Generic.Client.Rekey
hidden: true
tags: [Client Artifact]
---

This artifact forces the client to reinitialize it's client id.

It is normally not needed! You will want to use this artifact in
very specific situation, such as the Velociraptor service was
accidentally incorporated into a VM image with an existing write
back file. This will cause multiple systems to connect with the same
client id, and the server will reject clients with a HTTP 409
Rejected message.

If this happens, you can use the Server.Monitor.ClientConflict
artifact to schedule this artifact automatically.

The Wait parameter controls how long we wait before restarting the
client. Reduce this number if you need to rekey a lot of clients
quickly.


```yaml
name: Generic.Client.Rekey
description: |
  This artifact forces the client to reinitialize it's client id.

  It is normally not needed! You will want to use this artifact in
  very specific situation, such as the Velociraptor service was
  accidentally incorporated into a VM image with an existing write
  back file. This will cause multiple systems to connect with the same
  client id, and the server will reject clients with a HTTP 409
  Rejected message.

  If this happens, you can use the Server.Monitor.ClientConflict
  artifact to schedule this artifact automatically.

  The Wait parameter controls how long we wait before restarting the
  client. Reduce this number if you need to rekey a lot of clients
  quickly.

required_permissions:
  - EXECVE

parameters:
  - name: Wait
    description: Wait this long before restarting the client.
    type: int
    default: '10'

sources:
  - query:
      SELECT rekey(wait=Wait) FROM scope()

```
