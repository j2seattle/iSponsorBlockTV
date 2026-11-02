---
title: "Service Review: isponsorblocktv, 2026-11-02"
version: 1.0.0
status: active
last_edited_by: Claude Code
last_edited_date: 2026-11-02
audience: internal
tags: [yingson-labs, isponsorblocktv, service-review, findings]
summary: "First review of isponsorblocktv, part of the 2026-11 baseline run. Read only. 18 findings."
doc_type: reference
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
agent_context: true
permission_tier: n-a
last_verified: 2026-11-02
---

# Service Review: `isponsorblocktv` (container on CT 108 (docker-host)), 2026-11-02

First review. Read only: nothing was changed on any server.

## Basis

lab-standards `c0c6b8c`, repo `ceaf4e1`, app version iSponsorBlockTV v2.11.0 (image ghcr.io/dmunozv04/isponsorblocktv:v2.11.0, image id d1e876636754). Run: `director-worker/docs/charters/2026-11-baseline-reviews/`.

## Result

Critical 0, high 0, medium 6, low 12. Readiness R0. Connected C1. Findings: [FINDINGS.md](FINDINGS.md).

## Service card

- **Logo:** sponsorblock.svg
- **What it does:** Skips sponsor segments and mutes ads in the YouTube app on smart TVs. It runs as one small container that joins the TV's YouTube session and asks the SponsorBlock site where the sponsor parts are.
- **How the lab uses it:** Paired with the YouTube app on the Living Room TV and the Office TV. It runs all day in the background; Jason confirmed skips on both TVs on 2026-10-08 and 2026-10-09. Nothing else in the lab depends on it.

## Fact sheet

| Field | Value | Source |
|---|---|---|
| Service card | Skips sponsor segments and mutes ads in the YouTube app on the Living Room TV and the Office TV. Logo sponsorblock.svg. | doc: lab-standards/TOOLS.yaml:515-516; commandcenter/src/lib/serviceIcons.ts:87 |
| Slug, display name, owner | isponsorblocktv, iSponsorBlockTV, Media | doc: lab-standards/SERVICE-SLUG-REGISTRY.md:129; SERVICE-INVENTORY.md:43 |
| Guest id, type, privileged | Not a guest. Container isponsorblocktv on CT 108 (docker-host), compose project isponsorblocktv. Container Privileged=false. Host facts belong to the docker-host review. | live: docker inspect --format (Privileged false); docker compose ls |
| IP, ports, internal name, public name | No port, no name, no own IP. Shares 192.168.30.106 with the host. Bridge network isponsorblocktv_default, not host mode. | live: docker ps -a (empty Ports column); docker inspect NetworkMode |
| Off-LAN access class | N/A: no remote surface. Outbound only to youtube.com and sponsor.ajay.app. | doc: ARCHITECTURE.md section 3; LAB-STANDARDS.md:1465 |
| SSH alias and whoami | No alias. CT 108 has no jason user (Gate 2 fails). Belongs to the docker-host review (SEC-01 there). Reads here went through labread's pct exec path. | doc: findings/docker-host.json SEC-01 |
| Guest baseline | Belongs to the docker-host review (hostname docker, timezone ok, clock synced, cron enabled, jason user missing). | doc: findings/docker-host.json fact sheet |
| OS and kernel | Belongs to the docker-host review (Debian 13.7, kernel 6.17.9-1-pve). | doc: findings/docker-host.json |
| App version, upstream, pinning, last version check | Running v2.11.0 (image tag). Upstream latest v2.11.0, published 2026-08-21. Pinned by tag in compose; UPSTREAM.txt records the digest and the live image id matches its first 12 characters (d1e876636754). Checked 2026-11-02. v2.11.0 fixes a DIAL auto-discovery SSRF (GHSA-56v3-jrq2-4q9m); this install does not use auto-discovery. | live: labread get api.github.com/repos/dmunozv04/iSponsorBlockTV/releases/latest; docker images; doc: UPSTREAM.txt, docker-compose.yml:7 |
| Containers, image tags and age | 1 container: isponsorblocktv ghcr.io/dmunozv04/isponsorblocktv:v2.11.0, Up 4 days (daemon restart 2026-10-28 20:25 UTC, RestartCount 0, policy unless-stopped), image created 2 months ago. | live: docker ps -a; docker images; docker inspect --format |
| CPU, memory, swap, disk | Container: 0.00% CPU, 15.75 MiB of 4 GiB, 2 processes, net 74.8 MB in / 57.7 MB out, block 45.6 MB / 36.3 MB. No per-container series in Prometheus. Guest numbers belong to the docker-host review. | live: docker stats --no-stream; Prometheus __name__ list (no container resource series) |
| Mounts | One bind: /opt/isponsorblocktv/data to /app/data, read-write, on the CT 108 root disk. No NFS, no named volume. The data folder holds config.json (717 bytes, mode 644 root:root, modified 2026-10-09). | live: docker inspect Mounts; ls -la /opt/isponsorblocktv/data; stat |
| Depends on, and depended on by | Depends on docker-host, YouTube and sponsor.ajay.app. Nothing in the lab depends on it. docker-host row lists it as lost if CT 108 dies. | doc: SERVICE-DEPENDENCIES.md:85,89; ARCHITECTURE.md section 5 |
| API, credentials, MCP | API none, auth n-a, MCP n-a (do not add), tier read-only. No Cursor, Claude Code or Hermes credential. Vault path for the config file: vault://dashlane/yingson-labs/isponsorblocktv/config (not yet filled, per ARCHITECTURE section 16). | doc: SERVICE-INVENTORY.md:291; ARCHITECTURE.md sections 8, 12, 16 |
| Kuma monitors | None, by decision. No monitor_status series matches the slug. | live: Prometheus monitor_status regex .*isponsorblocktv.* and .*(sponsor\|docker).* (empty); doc: ARCHITECTURE.md section 11 |
| Telemetry | Container state is in Prometheus as docker_container_info{container="isponsorblocktv"} (state running, job integrations/unix) but its service label is docker-host, not the slug. Logs reach Loki through the journal (container label isponsorblocktv): 2 lines in 24 h, 4,221 in 30 d. TelemetryMissing covers the host, not this container. | live: Prometheus docker_container_info; Loki count_over_time |
| Homarr tile | None by decision (nothing to click). Not read live. | doc: ARCHITECTURE.md section 11; unknown for a live read |
| PBS | In the nightly CT 108 job. Newest CT 108 backup 2026-11-01 10:04 UTC (host review). The job excludes /var/lib/containerd per the doc; the image here is public and re-pullable. | doc: findings/docker-host.json fact sheet PBS; BAK-01 there |
| Kopia | No Kopia source for this slug. ARCHITECTURE says 'no additions'; LAB-STANDARDS section 6 has no line for it. | live: Prometheus kopia_source_* (no match for the slug); doc: ARCHITECTURE.md section 7 |
| Last restore test (Gate 7) | None for this container. The CT 108 scratch restore of 2026-10-07 (ct/108/2026-10-07T09:04:40Z) predates the container, which was installed 2026-10-08. | doc: restore-tests/restore-log.jsonl:31; ARCHITECTURE.md section 10 |
| Readiness | Inventory R1 ('deliberate'). Live checks support R0 under the cumulative rule: Gate 2 fails on CT 108 (no jason user, no alias). | doc: SERVICE-INVENTORY.md:75; live: findings/docker-host.json SEC-01 |
| Open incidents, WISH, past findings | No incidents. WISH-020, WISH-128, WISH-136, WISH-148, WISH-174 touch it. First review. | doc: INCIDENT-LOG.md (no match); WISHLIST.md:1857 and others |
| Repo and docs | Gitea jason/iSponsorBlockTV, laptop folder isponsorblocktv, HEAD ceaf4e1 (2026-10-29), nothing unpushed. Working tree has 7 modified files and 1 new file not committed. validate-docs.py: PASS (8 files). Docs last_verified 2026-10-08 to 2026-10-09. | live: git -C isponsorblocktv log/status; labread check validate-docs.py |
| Container health in the logs | Log runs from 2026-10-08 to 2026-11-01 (6,651 lines). Counts: 1,770 'Connected to device', 334 'Playing video' (118 with 0 segments), 11 'Skipping segment', 58 'Refreshed auth', 8 ERROR, 37 WARNING. Last Connected 2026-10-26 20:21 UTC; last video 2026-10-25; last skip 2026-10-17; auth refresh for two devices every day through 2026-11-01 20:25. | live: docker logs --since 800h --tail 9999 isponsorblocktv, counted with grep -c on the laptop (lines not read) |

## Service pass checklist

| Row | Status | What it says | Finding |
|---|---|---|---|
| Readiness | Not done | Docs say R1 (deliberate). The live checks support R0 under the cumulative rule: Gate 2 fails on CT 108 (no jason user, no Host alias; docker-host SEC-01), so even R1 is not met. Gate 3 met (one bind on the root disk). Gate 4 N/A (no name, no listener). Gate 5: no alert covers the container, its pairing, or its logs, so a likely failure would not alert; at most R2 even after Gate 2 (MON-01). Gate 6 docs exist but are behind (DOC-01, DOC-02). Gate 7: no restore test since the container existed (BAK-02). Gate 8: one sample, no thresholds (SPD-01). Standards line: not all met. N/A with reason: DNS, NPM, Kuma, Homarr and UniFi reservation (no port; R1 deliberate per SERVICE-INVENTORY.md:75). Deferred: Gate 2 (Jason, docker-host SEC-01), Dashlane copy (Jason, BAK-01), Gate 7 and Gate 8 (Chief). File-level list: Kopia none ('no additions' is not yet in section 6); PBS has the whole of CT 108 including /opt/isponsorblocktv/data/config.json; nothing stays on the external drive. Left before R4: close Gate 2 on the host, a log-based alert (MON-01), baselines (SPD-01), a restore test that checks config.json (BAK-02), and current docs. | DOC-03 |
| Updates | Already current | Running v2.11.0 (image tag and image id d1e876636754), upstream latest v2.11.0 (GitHub releases, 2026-08-21). v2.11.0 carries the fix for a blind SSRF in DIAL auto-discovery (GHSA-56v3-jrq2-4q9m); this install does not use auto-discovery. Nothing was applied. No section 12 backlog row yet (PAT-01). | PAT-01 |
| API | None | No API and no login. Auth n-a, tier read-only. Neither Cursor, Claude Code nor Hermes holds a credential. The only secret is config.json on CT 108; vault path vault://dashlane/yingson-labs/isponsorblocktv/config is named but not filled (BAK-01). |  |
| MCP | Do not add | There is no API to wrap, and the file can remote-control two TVs. Nothing an agent needs to do here. |  |
| Restore | Not done | No restore of a backup that contains this container. The only CT 108 scratch restore (ct/108/2026-10-07T09:04:40Z, pass, destroyed) predates it. Next: fold a config.json check into the docker-host restore test (BAK-02). | BAK-02 |
| Suggestions | Not done | 1. Turn the daily 'Refreshed auth' log line into a heartbeat and alert on it, and show last activity in Command Center (MON-01, INT-01, CC-01). 2. Mask lounge tokens before logs reach Loki and make config.json mode 600 (SEC-02, SEC-01). Not started in this review. | MON-01 |
| Connected | Done | C1: container state reaches Prometheus (docker_container_info) and the service has a TOOLS.yaml row and icon for Command Center; the Command Center page itself was not read, and the metric carries the host's service label, not the slug (CC-01). Not C2: nothing alerts on the container's own failures (MON-01). Not C3: no shared join key except the container name. Not C4: no API and no credential, by design. To reach C2: the log-heartbeat alert; C3: join on container name or compose_project to the slug and to docker-host. | MON-01 |

## Standards scorecard

| Section | Match | No | N/A | Unverified |
|---|---|---|---|---|
| A | 3 | 1 | 1 | 0 |
| B | 4 | 2 | 3 | 1 |
| C | 1 | 2 | 1 | 0 |
| D | 3 | 0 | 1 | 1 |
| E | 1 | 3 | 2 | 0 |
| F | 1 | 2 | 3 | 0 |
| G | 1 | 3 | 1 | 1 |
| H | 4 | 4 | 0 | 0 |
| I | 0 | 0 | 1 | 0 |
| J | 0 | 1 | 0 | 0 |

## Section A

Running v2.11.0 equals upstream latest v2.11.0. Tag pin, digest recorded and matching. No floating tag. No section 12 row. OS patches belong to the docker-host review.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| Track versions, never auto-update, floating tags are a finding | LAB-DEC-022 | v2.11.0 pinned by tag, no floating tag, equals upstream latest | yes |  |
| Upgrade backlog row in section 12 with source and pinning | LAB-STANDARDS §12 | No row found for the slug | no | PAT-01 |
| App version against upstream, release notes read for security fixes | SERVICE-REVIEW A | No gap; v2.11.0 includes the DIAL SSRF fix GHSA-56v3-jrq2-4q9m | yes |  |
| Image pin recorded and matches the running image | ARCHITECTURE §2 | UPSTREAM.txt digest prefix matches image id d1e876636754 | yes |  |
| OS packages current, reboot handled | SERVICE-REVIEW A | Host item: 3 pending, 2 security | n/a | belongs to the docker-host review (PAT-01 there) |

## Section B

No port, no name, outbound only, bridge network, not privileged, no socket. The pairing file is mode 644 and tokens are written to logs and Loki. Compose sets no hardening options. Host SSH, firewall and jason user are the docker-host review's.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| No public name; Cloudflare, NPM and Tailscale N/A for a no-port service | LAB-STANDARDS §4 | No published port, no name | yes |  |
| Off-LAN access class recorded | LAB-DEC-034 | N/A: no remote surface | n/a | no listener; LAB-STANDARDS.md:1465 |
| Listening ports bound only as needed | SERVICE-REVIEW B | None published | yes |  |
| No privileged container, socket mount or host network | LAB-STANDARDS §9 | Privileged false, bridge network, one data bind, no socket | yes |  |
| Secret files are mode 600 and gitignored | LAB-STANDARDS §9 | config.json 644 root:root; gitignored | no | SEC-01 |
| Secrets not written to logs | LAB-STANDARDS §9 | Lounge tokens in docker logs and in Loki | no | SEC-02 |
| Least privilege inside the container | LAB-STANDARDS §9 | Compose sets no user, cap_drop or read_only | unverified | SEC-03; runtime values not readable through labread |
| Secrets scan of the repo | LAB-STANDARDS §9 | git grep for secret-shaped strings: no match; no (verify) markers | yes |  |
| jason user, SSH hardening, guest firewall | Gate 2 | Host items | n/a | belongs to the docker-host review (SEC-01 to SEC-03 there) |
| Keys per consumer | LAB-STANDARDS §9 | No API, no key | n/a | no credential for any agent |

## Section C

15.75 MiB, 0.00% CPU, 2 processes against a 4 GiB guest. Nothing to size. One old sample in BASELINES.md and no Prometheus series for the container.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| BASELINES.md exists and has numbers with thresholds | Gate 8 | One memory sample, thresholds not set | no | SPD-01 |
| Container CPU and memory measured over time | Gate 8 | docker stats once: 15.75 MiB, 0.00% CPU; no Prometheus series | no | SPD-01 |
| Sizing matches use | SERVICE-REVIEW C | 15.75 MiB of a 4 GiB guest; not over-sized | yes |  |
| Databases on local disk | Gate 3 | No database; config on the CT 108 root disk | n/a | no database |

## Section D

Shows the sponsor skip on two TVs. Talks to YouTube and sponsor.ajay.app only. Wired to docker-host (both directions in the dependency doc), logs to Loki through the journal, container state to Prometheus. No API, no MCP, no credential. Nothing worth adding beyond log-derived counters.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| Dependency edges both directions | SERVICE-DEPENDENCIES | isponsorblocktv -> docker-host (line 89) and docker-host 'lost' list (line 85) | yes | YouTube and sponsor.ajay.app are outside the lab |
| Table 2 API and MCP status | LAB-DEC-017 | none, n-a, n-a, read-only; no credential for any agent | yes |  |
| Logs in Loki, metrics in Prometheus | SERVICE-REVIEW D | Loki container label present; docker_container_info present under service=docker-host | yes | CC-01 for the label |
| Homarr tile | LAB-STANDARDS §8 | None by decision | n/a | nothing to click; not read live |
| Gitea repo and GitHub mirror | SERVICE-REVIEW D | Gitea origin jason/iSponsorBlockTV, nothing unpushed; mirror not checked | unverified | push_mirrors needs a Gitea API read, not on the allowlist (WISH-018 covers the audit) |

## Section E

No Kuma, no Homarr, no alert rule covers it, by decision. The daily auth-refresh line (two per day) can serve as a heartbeat. The SRE catalog good-check has no stored reading and would fail today on its 'recent Connected' wording.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| DNS monitor and IP monitor | LAB-STANDARDS §7 | None; no name or IP of its own | n/a | N/A for a no-port service per SERVICE-INVENTORY.md:75; see the monitoring conflict |
| A likely failure raises an alert to #alerts through Alertmanager | LAB-DEC-061 | No rule covers the container or its logs | no | MON-01 |
| Discord down and up tested with a date | Gate 5 | None; nothing to fail | no | MON-01 |
| SRE catalog good-check with a stored reading | SERVICE-REVIEW E | Row exists; no stored reading; check would fail today | no | MON-02 |
| Health endpoint reports something real | LAB-STANDARDS §7 | No listener | n/a | no listener |
| Telemetry and TelemetryMissing cover it | Gate 5 | Host covered; the container's state series carries the host's service label | yes | host rule; CC-01 for the slug join |

## Section F

In the PBS CT 108 job (newest 2026-11-01). config.json is the only data; no Kopia, no Dashlane copy, no section 6 line. No restore test since the container existed.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| PBS nightly job includes the guest | LAB-DEC-088 | CT 108 newest 2026-11-01 10:04 UTC (host review) | yes |  |
| Everything needed to recover is in a backup plan, with a protect list or 'no additions' in section 6 | LAB-DEC-088 | config.json only in PBS; no line in section 6; Dashlane copy not done | no | BAK-01 |
| Restore tested with a date | Gate 7 | None for this container | no | BAK-02 |
| Docker named volumes captured | Gate 3 | No named volume | n/a | bind mount only |
| Freshness monitor for any backup job | LAB-DEC-053 | No Kopia job | n/a | no Kopia job |
| Snapshots within LAB-DEC-079 | LAB-DEC-079 | Not a guest | n/a | host item |

## Section G

Up 4 days, no restarts, 0 CPU. Daily auth refresh for both devices shows the process and credentials alive. No Connected line since 2026-10-26 and no video since 2026-10-25; cannot tell an idle TV from a lost pairing from the logs. Jason can answer in one minute.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| Container running, no restart loop | SERVICE-REVIEW G | Up 4 days, RestartCount 0, policy unless-stopped | yes |  |
| Connected to a TV recently | SERVICE-REVIEW G | Last Connected 2026-10-26; daily auth refresh continues | unverified | HLT-01 |
| Errors in 7 days readable and explained | SERVICE-REVIEW G | 1 ERROR in the last 7 days (2026-10-26 20:25 UTC); 8 over the whole log, transient | no | HLT-02 (no health check) |
| Readiness matches the gates, not rounded up | SERVICE-READINESS | R1 recorded; R0 supported | no | DOC-03 |
| Appendix A conformance walk | LAB-STANDARDS Appendix A | Met: slug, repo, docs, host, restart policy. N/A: DNS, NPM, Kuma, Homarr (no port; deliberate). Deferred: Gate 2 (docker-host), Gate 7, Gate 8 thresholds, Dashlane copy | no | DOC-03, BAK-01, BAK-02, SPD-01 |
| Guest baseline (hostname, timezone, clock, jason user, cron) | LAB-STANDARDS §3 | Host items | n/a | belongs to the docker-host review |

## Section H

validate-docs PASS. Docs are 3 to 4 weeks old and behind on figures, mode, restart, readiness. 8 repo files are edited but uncommitted. The sync receipt is behind. The preflight cannot find the repo.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| validate-docs.py exits 0 | SERVICE-REVIEW H | PASS, 0 errors, 0 warnings, 8 files | yes |  |
| ARCHITECTURE and BASELINES match the live container | SERVICE-REVIEW H | Memory, swap, mode and restart note differ | no | DOC-01 |
| TOOLS.yaml entry with what and lab_use | LAB-DEC-087 | Present, version v2.11.0, status in-progress | yes |  |
| Inventory readiness matches live | SERVICE-READINESS | R1 against R0 | no | DOC-03 |
| Sync receipt current, edits committed | LAB-DOC-CONTRACT | 575bf04 against c0c6b8c; 8 uncommitted files | no | DOC-02 |
| last_verified under 180 days | LAB-DOC-CONTRACT | Oldest 2026-10-08 (25 days) | yes |  |
| parent_doc is lab-standards/ARCHITECTURE.md | LAB-DOC-CONTRACT | Yes in ARCHITECTURE.md and CLAUDE.md | yes |  |
| Slug maps to its repo in the review tools | SERVICE-REVIEW H | Preflight says no repo | no | DOC-04 |

## Section I

Core features are on. Channel whitelist, minimum length and extra categories are off on purpose. Feature list is from the lab runbook only.

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| Features in use | SERVICE-REVIEW I | Core features on; whitelist, minimum length, other categories off | n/a | USE-01; nothing clearly worth adding |

## Section J

Command Center has the TOOLS.yaml row and icon. The container metrics carry service=docker-host. Container name and compose_project are the only join keys. No deep link exists (no UI).

| Standard | Source | Live shows | Match | Note |
|---|---|---|---|---|
| Visible and reachable from Command Center | SERVICE-REVIEW J | TOOLS.yaml row and icon only; container metrics carry the host label | no | CC-01 |

## What it could send to Command Center

| What | How | Page | Refresh | Wish |
|---|---|---|---|---|
| Pairing heartbeat: time of the last 'Refreshed auth' line per device, shown as 'paired and alive' or 'silent for N hours' | Alloy loki.process counter on the container's logs, exported to Prometheus (like loki_process_custom_karakeep_*); fallback Loki count_over_time | Services > isponsorblocktv | 5 minutes | WISH-316 |
| Last video seen and skips counted this month | Same counter on 'Playing video' and 'Skipping segment' | Services > isponsorblocktv | 15 minutes | WISH-316 |
| Container state, image tag and version against upstream latest | Prometheus docker_container_info{container="isponsorblocktv"} plus the GitHub releases API for dmunozv04/iSponsorBlockTV | Services > isponsorblocktv, and Patching | 1 minute for state, daily for upstream | WISH-316 |

## Join keys and deep links

| Key | Value | Command Center joins on it |
|---|---|---|
| slug | isponsorblocktv | False |
| container name | isponsorblocktv | False |
| compose project | isponsorblocktv | False |
| guest id | 108 (shared with docker-host) | True |
| host label | docker (Prometheus host label; service label is docker-host) | True |
| ip | 192.168.30.106 (shared; no own IP) | True |

| Task | URL | Works |
|---|---|---|
| See the container, its logs and restart it | https://portainer.yingson.com (container list; the container page URL needs the container id) | False |
| Open the project or its releases | https://github.com/dmunozv04/iSponsorBlockTV/releases | True |
| Open the lab repo | https://gitea.yingson.com/jason/iSponsorBlockTV | False |
| Look up a video's segments on SponsorBlock | https://sponsor.ajay.app | True |

## Correlations

| Purpose | With | What | Join key |
|---|---|---|---|
| troubleshooting | docker-host, loki, prometheus | When skips stop: container state, last 'Refreshed auth' time, the last subscribe error, and CT 108 swap and I/O pressure in one view, to tell a dead pairing from a stuck guest | container name isponsorblocktv / compose_project |
| building | docker-host, pbs, kopia | What this container still lacks: a log-based alert, a protect-list line, a restore check of config.json, a stored good-check reading | slug |
| insight | loki, lab-standards | Quiet periods against YouTube use: weeks with no video (2026-10-12 to 2026-10-16, 2026-10-20 to 2026-10-24, 2026-10-26 onward) and whether the TVs were simply off; tokens appearing in logs elsewhere in the lab | container name |
| alerting | prometheus, alertmanager | Container stopped or pairing silent for 26 h, one alert with the cause named, to #alerts | slug |

## What it could do for the rest of the lab

| Who gains | What | Effort | Check first | Wish |
|---|---|---|---|---|
| Alertmanager and #alerts | One alert that names the cause when the pairing heartbeat stops: container state from Prometheus plus the silent log counter, instead of a person noticing a missed skip | M | Whether Alloy on CT 108 can add a loki.process counter without restarting the other containers; Jason's answer on MON-01 about the R1 decision | WISH-316 |
| Alloy and every no-port container (gluetun, flaresolverr, unpackerr, decluttarr) | A shared pattern: mask secrets in logs before they reach Loki and count a heartbeat line per container | M | Which log lines carry tokens in each of those containers | WISH-355 |
| Aegis (version watch) | Add the image and tag to the version watch so a new iSponsorBlockTV release is noticed; the project releases every 1 to 2 months | S | Whether Aegis reads TOOLS.yaml version fields or Prometheus | WISH-316 |

## Wishlist verdicts

| Wish | Verdict | Note | Finding |
|---|---|---|---|
| WISH-020 | fold in | Restore-test rotation with an application check names iSponsorBlockTV. The config.json check becomes part of the docker-host restore test. | BAK-02 |
| WISH-128 | fold in | Plain-file secrets on guests. config.json mode 644 is one more file in the same class; step 1 (make root-only) covers it. | SEC-01 |
| WISH-136 | fold in later | Kuma monitor gaps. Not for this service by decision (no listener); the container-state alert for CT 108 comes from docker-host MON-01. Waits on Jason's answer to MON-01. | MON-01 |
| WISH-148 | keep as idea | agentread-state on more Docker guests. Touches CT 108 and the agentread user; no change needed for this container, but SEC-01 mode 600 must not break it. |  |
| WISH-174 | fold in | Docker socket proxy exposing container secrets to local users. The Alloy proxy path and the log-masking in SEC-02 are the same family of problems. | SEC-02 |

## Conflicts to reconcile

| What disagrees | Source A | Source B | Why it matters | How to settle | Recommendation |
|---|---|---|---|---|---|
| The inventory says R1 'is deliberate' and a missing monitor is not a defect; the review standard says a failure that would not alert caps readiness at R2 and is a finding | SERVICE-INVENTORY.md:75, LAB-STANDARDS.md:2016, ARCHITECTURE.md section 16 (no DNS, NPM, Kuma, Homarr; Hermes told to skip it) | LAB-STANDARDS section 7 and Gate 5; SERVICE-REVIEW E; this run's rule that a likely failure with no alert caps readiness at R2 | A lost pairing or a stuck loop would go unnoticed until a sponsor plays, and the SRE watcher is told not to look at this service. | Ask Jason: is a log-derived Prometheus alert (no new name, port, Kuma monitor or tile) acceptable under the R1 decision? | Yes. It respects every item the decision excludes and closes the gap. Keep no Kuma, NPM, Homarr or reservation. |
| Docs record R1; the live Gate 2 check on CT 108 fails, which gives R0 | SERVICE-INVENTORY.md:75 and SERVICE-READINESS.md:209 (R1) | Live: no jason user and no Host alias on CT 108 (docker-host SEC-01); cumulative readiness rule | The level shown in Command Center overstates the state, and the same Gate 2 failure also sits under Portainer, CursorScope and Quartermaster. | Jason answers docker-host SEC-01: add a jason user to CT 108, or waive Gate 2 for guests managed through pct exec. | Record R0 for now and close docker-host SEC-01; then R1 holds at no cost to this container. |

## What works

- The container is Up with no restarts, 0.00% CPU and 15.75 MiB of memory.
- Both paired TVs refresh their session every day (2 lines a day through 2026-11-01 20:25 UTC), so the process and credentials are alive.
- Skipping worked: 11 skips and 334 videos handled from 2026-10-08 to 2026-10-25, including the confirmed tests on both TVs.
- Running v2.11.0 is the latest upstream release and includes the SSRF fix. The pinned digest in UPSTREAM.txt matches the running image.
- No published port, bridge network, not privileged, no Docker socket, data folder gitignored; validate-docs passes.

## Not checked, and why

| Item | Why |
|---|---|
| Whether either TV is still paired and skipping right now | Logs cannot tell an idle TV from a lost pairing; a test needs a person at the TV (HLT-01). |
| Runtime CapDrop, ReadonlyRootfs, SecurityOpt, limits, the container's user | labread refused those docker inspect fields and docker exec (SEC-03). |
| Homarr tile, Command Center Services page, GitHub push mirror state | No Homarr read or Gitea API read on the allowlist; the Command Center page was not opened. |
| Whether an old lounge token still controls a TV, and whether Loki read is limited to the lab | Testing credentials is not a read; Loki access control was not examined (SEC-02). |
| Upstream documentation for v2.11.0 features and full release notes for older versions | Only the latest release metadata and tag list were read; the feature list comes from the lab runbook (USE-01). |
| Off-LAN test, UniFi rules, Docker daemon log driver, guest firewall | No remote surface; UniFi and host items belong to the unifi and docker-host reviews. |
| SPD timing runs by name and IP, Kuma history | No name, no port and no monitor exist to time. |

## Sprint plan

| Sprint | IDs | What changes | You approve | Time | How to check | How to undo |
|---|---|---|---|---|---|---|
| Sprint 0, doc fixes | PAT-01, MON-02, DOC-01, DOC-02, DOC-03, DOC-04 | Add the section 12 backlog row; change the SRE good-check wording; update ARCHITECTURE, BASELINES, TROUBLESHOOTING and CHANGELOG to the live facts; commit the working-tree edits and refresh the sync receipt; correct the recorded readiness; teach the preflight about the repo name. | Approve the readiness wording (R0 until docker-host SEC-01 closes, or R1 if Jason waives Gate 2). | About 1 hour | validate-docs.py exits 0; git status is clean; SERVICE-INVENTORY row matches. | git revert on each repo. |
| Sprint 1, small reversible changes | SEC-01, SEC-03, SPD-01, HLT-01, HLT-02, BAK-01, BAK-02 | Ask Jason to test a skip on both TVs; chmod 600 config.json; decide the config.json protection; add hardening options and a healthcheck to compose and recreate; record a baseline; include a config.json check in the docker-host scratch restore. | Jason: the HLT-01 answer, the BAK-01 choice, and an OK for a container recreate (a short break in skipping) and for the scratch restore. | About 2 hours, plus Jason's one-minute TV test | A 'Refreshed auth' line follows the chmod; docker ps shows (healthy); a video with a known sponsor skips on each TV; config.json present on the scratch CT. | chmod 644 and 755; restore the .bak compose file and recreate; destroy the scratch CT. |
| Sprint 2, patching |  | No patching: v2.11.0 is the latest release. OS updates are in the docker-host review. | Nothing. | None | Re-check upstream at the follow-up run. | Not applicable. |
| Sprint 3, security and exposure | SEC-02 | Mask lounge tokens in the Alloy pipeline on CT 108 and in labread's mask list. | Jason: Alloy restart after hours; whether to re-pair both TVs to rotate credentials. | About 1 hour | Loki count of 'lounge id token' lines with an unmasked value is 0 for new lines. | Revert the Alloy config and restart Alloy. |
| Sprint 4, integrations | MON-01, INT-01, CC-01, USE-01 | Log-heartbeat counters in Alloy, a Prometheus rule through Alertmanager to #alerts, a Command Center panel and slug join, and (optionally) extra skip categories. | Jason's answer on the R1 decision (MON-01); a Discord down and up test; each item is a WISH, not approval to build. | About half a day | The new counter increases daily; a test silence fires one alert in #alerts and clears. | Remove the Alloy stage, the rule and the panel. |
