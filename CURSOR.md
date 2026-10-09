---
title: Cursor briefing — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-08
parent: README.md
children: []
siblings: [CLAUDE.md, ARCHITECTURE.md, CHANGELOG.md]
audience: internal
tags: [yingson-labs, isponsorblocktv, agent-briefing]
summary: "Current state of the YouTube TV sponsor-skip trial on CT 108."
doc_type: agent-briefing
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-10-08
---

# Cursor — isponsorblocktv

## Now

v2.11.0 on CT 108. No port. Categories `sponsor`, `selfpromo`, `interaction`. One TV paired. Jason confirmed skips on `FrRDJb0WBf0`.

Lab repo: [https://github.com/j2seattle/iSponsorBlockTV](https://github.com/j2seattle/iSponsorBlockTV). Upstream: [dmunozv04/iSponsorBlockTV](https://github.com/dmunozv04/iSponsorBlockTV).

Readiness **R1**, on purpose. Do not add DNS, NPM, Kuma, or a UniFi reservation for this container. The host `192.168.30.106` is already reserved. Hermes is told the same limit.

## Do not

- Print lounge tokens from `docker logs` or from `config.json`.
- Commit `/opt/isponsorblocktv/data`.
- Switch the container to host networking.
- Treat "0 segments" as a failure.
