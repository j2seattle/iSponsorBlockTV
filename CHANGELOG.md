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
last_verified: 2026-10-09
---

# Changelog

All notable changes to **isponsorblocktv** are documented here.

**Prepend-only rule:** New sprints are added at the top. Never delete historical entries.

---

## [Unreleased]

- [OPS] **Sprint 2 re-check, lane FAM (2026-11-03 evening).** No Sprint 2 IDs for this service. Upstream latest is still v2.11.0, the version the compose file pins. The coordinator's Docker restart (S2-01, 21:07) recreated the container with json-file logs and the hardened compose file; this repo already matches (140d8e1). CT 108 was not touched.

- [DOCS] **Compose file matches live (2026-11-03).** The Sprint 1 hardening (cap_drop ALL, no-new-privileges, read-only root, tmpfs /tmp, 256 MB, 100 pids) was live on CT 108 but not in git. Copied in so the CT 108 after-hours restart deploys the same file.
- [OPS] **Sprint 1 fix run, lane FAM (2026-11-02 night, changes made 23:00 to 01:00 Pacific).** `config.json` is now mode 600 and its folder 700 (SEC-01). Compose file hardened: all capabilities dropped, no-new-privileges, read-only root, 256 MB memory limit, 100 process limit; container recreated once and running (SEC-03). A new baseline reading is in BASELINES.md. The TV test (HLT-01) and the backup choice (BAK-01) wait for Jason. Safety net: PBS backup 21 hours old and `docker-compose.yml.bak-s1-20261102`. `no decision`.

- [OPS] CT 108 RAM is 4096 MB as of 2026-10-11. This container was not restarted.

## [0.1.2] — 2026-10-09

- [OPS] A second TV is paired on the same container. Jason confirmed skips the same night. The Living Room TV and the Office TV are both protected. Skip categories stay shared. The YouTube app still reports both as "YouTube on TV".

## [0.1.1] — 2026-10-08

- [INTERNAL] [agent-context] Hermes SOUL and the service check now say R1 is deliberate. Missing DNS, NPM, Kuma, Homarr, and a UniFi reservation are not open work.

## [0.1.0] — 2026-10-08

- [OPS] Trial container on CT 108. Image `ghcr.io/dmunozv04/isponsorblocktv:v2.11.0`. No published port. One TV paired as "YouTube on TV".
- [OPS] Skip categories: `sponsor`, then `selfpromo` after the Grind Hard Plumbing Co opening played through, then `interaction` for like-and-subscribe reminders. Ad mute and ad skip on.
- [INTERNAL] [agent-context] Registered in lab-standards as `isponsorblocktv`, readiness **R1**. No own IP. Gitea `jason/iSponsorBlockTV`. GitHub `https://github.com/j2seattle/iSponsorBlockTV`.
