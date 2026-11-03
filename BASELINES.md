---
title: Baselines — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Claude Code
last_edited_date: 2026-11-02
parent: ARCHITECTURE.md
children: []
siblings: [TROUBLESHOOTING.md, CHANGELOG.md]
audience: ops
tags: [yingson-labs, isponsorblocktv, baselines]
summary: "One memory sample for the container. Thresholds are not set. No health URL."
doc_type: baselines
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-11-02
---

# Baselines — isponsorblocktv

**Slug:** `isponsorblocktv` · **Last full review:** 2026-11-02

Samples from 2026-10-08 (while a video played) and 2026-11-02 (idle). Not a range. Thresholds are not set. Guest numbers are the whole of CT 108.

## Measurement environment

| Field | Value |
|---|---|
| **Host** | CT 108, shared Docker host |
| **App version** | `ghcr.io/dmunozv04/isponsorblocktv:v2.11.0` |
| **Typical load** | not yet measured |
| **Measured over** | a single `docker stats` at 2026-10-08 17:05 PDT, and `free -m` on the guest at 16:53 PDT |

## Container memory

- **Normal range:** not yet measured. Three readings: 47.77 MiB while a video played (2026-10-08), 15.79 MiB idle (2026-11-02) and 50.35 MiB just after the hardened recreate (2026-11-03, limit now 256 MiB). Two more samples over a week are needed for a range.
- **Warning threshold:** 100 MiB (a starting guess, not measured)
- **Critical threshold:** not yet measured
- **Measurement source:** `sudo pct exec 108 -- docker stats --no-stream isponsorblocktv` from `jason@192.168.30.209`
- **Known false-positive conditions:** CT 108's other containers dominate guest memory. A guest-wide number is not this service.
- **Last verified:** 2026-11-02

## Container CPU

- **Normal range:** not yet measured. Two readings: 0.00% (2026-10-08) and 0.00% (2026-11-02).
- **Warning threshold:** not yet measured
- **Critical threshold:** not yet measured
- **Measurement source:** same `docker stats` as memory
- **Known false-positive conditions:** n-a
- **Last verified:** 2026-11-02

## Health endpoint

- **Normal range:** n-a
- **Warning threshold:** n-a
- **Critical threshold:** n-a
- **Measurement source:** the process has no listener
- **Known false-positive conditions:** `Playing video … with 0 segments` means SponsorBlock returned nothing. The container can still be healthy.
- **Last verified:** 2026-11-02

## Guest memory and swap

- **Normal range:** not yet measured. At 16:53 PDT on 2026-10-08 the guest had 84 MB available of 2048 MB, and swap was 511 MB of 512 MB. On 2026-10-11 RAM was raised to 4096 MB. Just after that, about 2.1 GB was available and swap was still 511 MB of 512 MB. On 2026-11-02 swap was 122 MB of 512 MB (`free -m`; the review read 111 MB). The full-swap figure is out of date (docker-host HLT-03).
- **Warning threshold:** not yet measured
- **Critical threshold:** not yet measured
- **Measurement source:** `sudo pct exec 108 -- free -m`
- **Known false-positive conditions:** swap was already full before this container was started.
- **Last verified:** 2026-11-02
