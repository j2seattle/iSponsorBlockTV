---
title: Troubleshooting — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-08
parent: ARCHITECTURE.md
children: []
siblings: [BASELINES.md, CHANGELOG.md]
audience: ops
tags: [yingson-labs, isponsorblocktv, troubleshooting]
summary: "Symptom-first notes for the YouTube TV sponsor skipper on CT 108."
doc_type: troubleshooting
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-10-08
---

# Troubleshooting — isponsorblocktv

**Slug:** `isponsorblocktv` · **Host:** CT 108 / 192.168.30.106

Guest pressure, DNS, and PBS belong in `lab-standards/TROUBLESHOOTING-CROSS-SERVICE.md`.

## A video's sponsor still plays

1. `sudo pct exec 108 -- docker logs --tail 50 isponsorblocktv` from `jason@192.168.30.209`. That `sudo` is on the hypervisor. Inside the guest the command is already root.
2. Find `Playing video <id> with N segments`.
3. **0 segments:** SponsorBlock has no submission in the enabled categories. The container is doing what it can. Look the id up at `https://sponsor.ajay.app` before changing config.
4. **N > 0 and no `Skipping segment` line:** playback never entered the segment, or a newer state cancelled the wait. On 2026-10-08, `FrRDJb0WBf0` had a paid sponsor at 10:47 and the TV left the video after about a minute.
5. **The segment's category is not enabled.** The opening of `FrRDJb0WBf0` is `selfpromo` (0:00–0:16). With only `sponsor` on, it played. `selfpromo` was added the same evening.

Do not paste the log's lounge token into chat.

## Ads mute, sponsors do not

That split is expected. Ad mute uses the YouTube ad state. Sponsor skip uses SponsorBlock. One working does not prove the other.

## Setup said "Paired" and then "Aborted"

The CLI wizard reads stdin to the end. A pipe that closes on the last prompt skips `config.json` save and consumes the TV code. Feed a file that ends with a blank line. See [docs/RUNBOOK.md](docs/RUNBOOK.md).

## Container is up and the TV does nothing

Look for `Connected to device`. If it is missing, the screen id was revoked. Pair again with a new code from the YouTube app, **Settings → Link with TV code**. Restarting the container does not create a new code.

## `Unknown state 1082`

Logged by `pyytlounge` while skipping an ad on 2026-10-08. The next line was still playback. Not a reason to recreate the container.
