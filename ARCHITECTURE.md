---
title: Architecture — isponsorblocktv
version: 0.1.0
status: in-progress
last_edited_by: Cursor Grok
last_edited_date: 2026-10-11
parent: README.md
children: []
siblings: [CHANGELOG.md, CURSOR.md, BASELINES.md, TROUBLESHOOTING.md]
audience: internal
tags: [yingson-labs, isponsorblocktv, architecture]
summary: "SponsorBlock lounge client for paired YouTube TV apps, as a container on CT 108."
doc_type: architecture
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
depends_on: [docker-host]
depended_on_by: []
agent_context: true
permission_tier: read-only
last_verified: 2026-10-08
---

# Architecture — iSponsorBlockTV

**Slug:** `isponsorblocktv` · **Status:** `in-progress` · **Host:** CT 108 / 192.168.30.106 · **Readiness:** R1

---

## Table of Contents

1. [Overview](#1-overview)
2. [Host & Runtime](#2-host--runtime)
3. [Network & Access](#3-network--access)
4. [Components](#4-components)
5. [Dependencies](#5-dependencies)
6. [Data Flow](#6-data-flow)
7. [Storage & Layout](#7-storage--layout)
8. [Configuration & Environment](#8-configuration--environment)
9. [Deployment](#9-deployment)
10. [Backup & Recovery](#10-backup--recovery)
11. [Monitoring & Health](#11-monitoring--health)
12. [API & Automation Profile](#12-api--automation-profile)
13. [Security](#13-security)
14. [Known Issues & Constraints](#14-known-issues--constraints)
15. [Operational Procedures](#15-operational-procedures)
16. [Open Items & Planned Changes](#16-open-items--planned-changes)

## 1. Overview

A pinned container of [iSponsorBlockTV](https://github.com/dmunozv04/iSponsorBlockTV) v2.11.0. It joins a paired TV's YouTube lounge session, asks SponsorBlock where the segments are, and sends skip or mute commands. Lab copy: [https://github.com/j2seattle/iSponsorBlockTV](https://github.com/j2seattle/iSponsorBlockTV).

One TV was paired on 2026-10-08. The YouTube app named it "YouTube on TV". Jason confirmed self-promo and paid-sponsor skips on Grind Hard Plumbing Co, `FrRDJb0WBf0`.

Readiness stays **R1**. The process works. It has no name, no TLS, no health URL, and no monitor, so R2 and R3 are not available. That is recorded in §3 and §11, not left blank.

## 2. Host & Runtime

| | |
|---|---|
| Guest | CT 108, hostname `docker`, `192.168.30.106` |
| Why this guest | One small outbound container. A dedicated CT would add a reservation, DNS, and a backup slot for no gain. |
| Runtime | Docker Compose, project directory `/opt/isponsorblocktv` |
| Image | `ghcr.io/dmunozv04/isponsorblocktv:v2.11.0` |
| Restart | `unless-stopped` |
| User | Container default. Not a published service account. |

CT 108 is a 4 GB LXC as of 2026-10-11 (it was 2 GB). On 2026-10-08 its swap was full before this container started. The container itself was 47.77 MiB at 17:05 PDT. See [BASELINES.md](BASELINES.md).

## 3. Network & Access

*Not applicable as a reachable lab name — the container publishes no port.*

Traffic is outbound to `youtube.com` and `https://sponsor.ajay.app/api/`. After pairing, the TV and this container do not need to be on the same LAN. Setup used a TV link code, not SSDP, so `network_mode: host` is not set.

| Surface | Decision |
|---|---|
| Own IP | None. Do not create a UniFi reservation. |
| Host reservation | Already present: `Docker LXC (Authentik)`, MAC `bc:24:11:92:d7:e2`, fixed IP `192.168.30.106` |
| AdGuard rewrite | N/A. No hostname. |
| NPM | N/A. Nothing to proxy. |
| Cloudflare | N/A. No public name. |
| Tailscale | N/A. No UI to reach. |
| Firewall allow | N/A. No inbound listener. |

## 4. Components

| Piece | Role |
|---|---|
| `isponsorblocktv` | One Python process. Polls the lounge, skips segments, mutes ads. |
| `config.json` | Device screen id plus skip categories. On the guest only. |
| YouTube lounge | The control channel. The TV's YouTube app is the player. |
| SponsorBlock API | Crowd-sourced segment times. Empty result means the video plays through. |

## 5. Dependencies

| Depends on | Why |
|---|---|
| `docker-host` | The container dies with CT 108. |
| YouTube | Lounge session and playback state. |
| `sponsor.ajay.app` | Segment lookup. |

Nothing in the lab depends on this container. Ad mute and sponsor skip stop. YouTube on the TV keeps playing.

## 6. Data Flow

1. A person plays a video in the YouTube app on a paired TV, or casts to that TV's YouTube app.
2. The container sees the video id and position on the lounge session.
3. It asks SponsorBlock for segments in `sponsor`, `selfpromo`, and `interaction`.
4. When playback reaches a segment, it seeks to the end. The log line is `Skipping segment`.
5. A YouTube ad mutes immediately and is skipped once the Skip button exists.

A phone playing YouTube locally is not this flow. AirPlay or screen mirroring is not this flow. A video with zero submitted segments plays in full. That is a miss in the crowd database, not a failed container.

## 7. Storage & Layout

| Path | What |
|---|---|
| `/opt/isponsorblocktv/docker-compose.yml` | Compose file on CT 108. The copy in git is the same text. |
| `/opt/isponsorblocktv/data/config.json` | Pairing and categories. Bind-mounted at `/app/data`. |
| Docker named volume | None. |

`config.json` is on the CT 108 root disk, so a PBS snapshot of CT 108 includes it. It is not a Docker named volume and it is not on NFS. No database.

Kopia file-level list: no additions. The path is already inside the CT 108 snapshot. Nothing for this service stays on the external drive.

## 8. Configuration & Environment

No environment file and no API key. Categories set on 2026-10-08:

| Key | Value |
|---|---|
| `skip_categories` | `sponsor`, `selfpromo`, `interaction` |
| `mute_ads` | true |
| `skip_ads` | true |
| `auto_play` | true |
| `skip_count_tracking` | true |
| `apikey` | empty. A YouTube Data API key is only required for a channel whitelist. There is no whitelist. |
| `sponsorblock_api_url` | `https://sponsor.ajay.app/api/` |

The screen id in `devices` is a lounge credential. It is not in git and not in Dashlane. Vault path if Jason copies the file himself: `vault://dashlane/yingson-labs/isponsorblocktv/config`.

## 9. Deployment

First install was 2026-10-08 from the laptop, over SSH to `jason@192.168.30.209`, then `sudo pct exec 108`. The image was pulled on the guest. Pairing used `docker run --rm -i ... --setup-cli` with answers from a file, because a bare pipe closed stdin before `config.json` was saved. The container is started with `docker compose -f /opt/isponsorblocktv/docker-compose.yml up -d`.

There is no guest checkout of this git repo. Outbound Gitea key: n-a (no deploy checkout).

## 10. Backup & Recovery

Nightly PBS includes CT 108, and therefore this directory. A scratch boot of `ct/108/2026-10-07T09:04:40Z` on 2026-10-07 proved the guest can boot. That snapshot predates this container, so it does not prove this service restores. Gate 7 for `isponsorblocktv` is not tested.

To recover the pairing, restore CT 108 or put `config.json` back and start the container. A lost config means pairing again with a new TV code. The procedure is in [docs/RUNBOOK.md](docs/RUNBOOK.md).

## 11. Monitoring & Health

*Not applicable as an HTTP health check — the process has no listener.*

A running container plus a log line `Connected to device` is the check. `Playing video <id> with 0 segments` is healthy when SponsorBlock has nothing for that video. There is no Uptime Kuma monitor: a DNS check and an IP check need a URL, and a Docker-socket monitor on Kuma is a separate security decision. No Homarr tile: there is nothing to click. No Discord down/up test, because there is no monitor to fail.

## 12. API & Automation Profile

| | |
|---|---|
| API type | `none` |
| Auth | `n-a` |
| MCP | `n-a` — do not add. There is no API. |
| Laptop env | None. No key names to leave blank. |
| Permission tier | `read-only` |
| Cursor / Claude / Hermes | No credential. |

Restarting the container or editing `config.json` is a human action on CT 108. No token is issued. Deleting `config.json` unpairs the TV and stays human.

## 13. Security

The container has no inbound port. v2.11.0 fixes a DIAL auto-discovery SSRF (GHSA-56v3-jrq2-4q9m). This install does not use auto-discovery.

`config.json` can remote-control the paired YouTube session. It stays on CT 108, mode whatever the root-owned file was written as, and is gitignored. Logs print lounge tokens. Do not paste container logs into chat or tickets.

## 14. Known Issues & Constraints

- CT 108 swap was full on 2026-10-08. RAM is 4096 MB as of 2026-10-11. This container is small. The guest is shared.
- YouTube revokes screen ids. A dead pairing needs a new TV code. The README of the upstream project said this was in progress in 2026.
- Segments exist only where someone submitted them. Category filter matters: a self-promo is not a sponsor.
- `pyytlounge` logged `Unknown state 1082` during ad skip on 2026-10-08. Playback continued.
- Shorts are only partly supported upstream.
- One device is paired. Its display name is the generic "YouTube on TV".

## 15. Operational Procedures

[docs/RUNBOOK.md](docs/RUNBOOK.md): restart, read logs, add a category, pair another TV.

## 16. Open Items & Planned Changes

- Gate 4 and Gate 5 stay N/A unless the app grows a health URL. Do not invent a hostname for a process with no port. Hermes is told this R1 state is deliberate.
- Gate 7: a PBS snapshot taken after 2026-10-08 has not been restored.
- Gate 8: one container memory sample. Warning and critical thresholds are not set.
- Dashlane copy of `config.json` is not done.
- More TVs, when Jason wants them.
- Intro, outro, preview, hook, exclusive access, filler, and music off-topic stay off.
