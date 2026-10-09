---
title: iSponsorBlockTV
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-08
parent: ARCHITECTURE.md
children: []
siblings: [ARCHITECTURE.md, CHANGELOG.md, CURSOR.md]
audience: internal
tags: [yingson-labs, isponsorblocktv]
summary: "SponsorBlock client for the lab's YouTube TV apps, running on the Docker host."
doc_type: readme
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-10-08
---

# iSponsorBlockTV

Skips sponsor reads, self-promos, and like-and-subscribe reminders on a paired TV's YouTube app, and mutes YouTube ads until Skip is available. It is a lounge remote, not a proxy. Phones and iPads playing YouTube themselves are out of scope.

Lab notes live here: [https://github.com/j2seattle/iSponsorBlockTV](https://github.com/j2seattle/iSponsorBlockTV)

The application itself is [dmunozv04/iSponsorBlockTV](https://github.com/dmunozv04/iSponsorBlockTV) v2.11.0. Gitea is `https://gitea.yingson.com/jason/iSponsorBlockTV`.

| | |
|---|---|
| Host | CT 108 `docker` · `192.168.30.106` |
| Address of its own | None. No port, no DNS name, no UniFi client. |
| Image | `ghcr.io/dmunozv04/isponsorblocktv:v2.11.0` |
| Skip categories | `sponsor`, `selfpromo`, `interaction` |
| Ads | Mute on, skip on |
| Reach | Outbound to YouTube and SponsorBlock only |

The Docker host reservation is already `Docker LXC (Authentik)` at `192.168.30.106` (MAC `bc:24:11:92:d7:e2`). Do not add a second reservation for this container.

Deploy, pairing, and restart steps are in [docs/RUNBOOK.md](docs/RUNBOOK.md).
