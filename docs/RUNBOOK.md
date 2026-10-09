---
title: Runbook — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-08
parent: ARCHITECTURE.md
children: []
siblings: [TROUBLESHOOTING.md]
audience: ops
tags: [yingson-labs, isponsorblocktv, runbook]
summary: "Restart, logs, categories, and pairing for the CT 108 container."
doc_type: runbook
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-10-08
---

# Runbook — isponsorblocktv

SSH to the hypervisor as `jason@192.168.30.209`. Every `pct` command needs `sudo`. After `sudo pct exec 108` the guest command runs as root, so do not add another sudo.

## See that it is connected

```bash
sudo pct exec 108 -- docker logs --tail 30 isponsorblocktv
```

A good line is `Connected to device`. Ignore the lounge token on that line. Do not copy logs into chat.

## Restart

```bash
sudo pct exec 108 -- docker restart isponsorblocktv
```

Restart after a category edit. Compose does not reload `config.json` by itself.

## Change skip categories

Edit `/opt/isponsorblocktv/data/config.json` on CT 108. The `skip_categories` array accepts `sponsor`, `selfpromo`, `interaction`, `intro`, `outro`, `preview`, `hook`, `exclusive_access`, `filler`, and `music_offtopic`. Then restart. Current set is `sponsor`, `selfpromo`, `interaction`.

## Pair another TV

On the TV: YouTube app → **Settings → Link with TV code**. The code expires quickly.

Write the code into an answers file on the guest that ends with a blank line, then:

```bash
sudo pct exec 108 -- docker run --rm -i \
  -v /opt/isponsorblocktv/data:/app/data \
  ghcr.io/dmunozv04/isponsorblocktv:v2.11.0 \
  --setup-cli < /tmp/isb-answers.txt
```

The wizard asks, in order: proxy, add a device, the code, more devices, YouTube API key, categories, channel whitelist, minimum length, report skips, mute ads, skip ads, autoplay, SponsorBlock URL. An existing `config.json` makes some of those "change it?" questions. Read the prompts. A closed pipe on the last question prints `Aborted` and does not save.

Delete the answers file when it finishes. It contains the TV code.

## Restore

PBS of CT 108 is the backup. No snapshot taken after this container existed has been restored. A restore of the guest brings `/opt/isponsorblocktv` back with every other container on that guest. If only `config.json` was lost, pair again.
