---
title: "iSponsorBlockTV skip activity and unused settings"
version: 0.1.0
status: proposed
last_edited_by: Claude Code
last_edited_date: 2026-11-05
audience: internal
tags: [yingson-labs, isponsorblocktv, use-finding]
summary: "What the lab could read from iSponsorBlockTV's logs, and which unused settings are worth turning on (INT-01, USE-01)."
doc_type: reference
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
agent_context: true
permission_tier: n-a
last_verified: 2026-11-05
---

# iSponsorBlockTV skip activity and unused settings

Proposal only. Nothing is built or changed live. Written by push 06, lane DOCS-B, 2026-11-05.

## Skip activity (INT-01)

The container logs "Connected to device", "Playing video" and "Skipping segment". From 2026-10-08 to 2026-11-01 the log showed 334 videos played, 11 skips, and the last skip on 2026-10-17. Nothing in the lab keeps these counts.

Proposal: add an Alloy `loki.process` stage on CT 108 that turns those three line types into Prometheus counters, the same way the existing error-burst counters work. Command Center can then show "last video seen" and "skips this month" on the Services page. An alert on "no video seen for N days" fits the freshness pattern in WISH-316. No new service and no MCP. Undo: remove the Alloy stage.

Building this is tracked by WISH-316 (alert when a feed or sync stops flowing). It touches the shared Alloy config on CT 108, so it waits for the alerting session.

## Unused settings (USE-01)

Used today: two devices, three categories (sponsor, selfpromo, interaction), mute ads, skip ads, autoplay, report skips. Not used: channel whitelist (needs a YouTube Data API key, none issued), minimum skip length, proxy, and seven other categories (intro, outro, preview, hook, exclusive_access, filler, music_offtopic), which are off on purpose (ARCHITECTURE section 16).

Recommendation: no change. If Jason wants more skipping for family viewing, `intro` and `outro` are the likeliest next categories. That is an edit to `config.json` plus a container restart, so it needs Jason's yes. The upstream v2.11.0 documentation was not read, so the list comes from the lab runbook and may miss features.
