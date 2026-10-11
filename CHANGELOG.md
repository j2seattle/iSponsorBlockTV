---
title: Changelog — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-11
parent: README.md
children: []
siblings: [README.md, CURSOR.md, ARCHITECTURE.md]
audience: internal
tags: [yingson-labs, isponsorblocktv, changelog]
summary: "All notable changes to isponsorblocktv. Prepend only — never delete entries."
doc_type: changelog
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: []
depended_on_by: []
agent_context: false
permission_tier: n-a
last_verified: 2026-10-08
---

# Changelog

All notable changes to **isponsorblocktv** are documented here.

**Prepend-only rule:** New sprints are added at the top. Never delete historical entries.

---

## [Unreleased]

- [OPS] CT 108 RAM is 4096 MB as of 2026-10-11. This container was not restarted.

## [0.1.1] — 2026-10-08

- [INTERNAL] [agent-context] Hermes SOUL and the service check now say R1 is deliberate. Missing DNS, NPM, Kuma, Homarr, and a UniFi reservation are not open work.

## [0.1.0] — 2026-10-08

- [OPS] Trial container on CT 108. Image `ghcr.io/dmunozv04/isponsorblocktv:v2.11.0`. No published port. One TV paired as "YouTube on TV".
- [OPS] Skip categories: `sponsor`, then `selfpromo` after the Grind Hard Plumbing Co opening played through, then `interaction` for like-and-subscribe reminders. Ad mute and ad skip on.
- [INTERNAL] [agent-context] Registered in lab-standards as `isponsorblocktv`, readiness **R1**. No own IP. Gitea `jason/iSponsorBlockTV`. GitHub `https://github.com/j2seattle/iSponsorBlockTV`.
