---
title: "Findings: isponsorblocktv"
version: 1.0.0
status: active
last_edited_by: Claude Code
last_edited_date: 2026-11-02
audience: internal
tags: [yingson-labs, isponsorblocktv, service-review, findings]
summary: "Rolling list of Service Review findings for isponsorblocktv. Run 1, 2026-11-02: 18 findings."
doc_type: reference
scope: service
service_name: isponsorblocktv
parent_doc: lab-standards/ARCHITECTURE.md
agent_context: true
permission_tier: n-a
last_verified: 2026-11-02
---

Page: https://claude.ai/artifact/VQ9GcTxCT3wsUCbQTF6k9H (private)

# Findings: isponsorblocktv

Process: lab-standards `docs/SERVICE-REVIEW.md` · Run log: lab-standards `service-reviews/review-log.jsonl` · Full review: [2026-11-02-service-review.md](2026-11-02-service-review.md)

| Run | Date | Kind | Counts | File |
|---|---|---|---|---|
| 1 | 2026-11-02 | review (read only, nothing changed on any server) | critical 0, high 0, medium 6, low 12 (18 total) | [2026-11-02-service-review.md](2026-11-02-service-review.md) |

## Findings

| ID | Sev | Title | Standard | Owner | Effort | Approval | Your call | Checked | State |
|---|---|---|---|---|---|---|---|---|---|
| SEC-01 | medium | config.json holds the TV pairing credentials and is readable by every user on CT 108 | LAB-STANDARDS §9 | jason | S | remediate | No | verified | fixed (verified) 2026-11-02 |
| SEC-02 | medium | The container writes lounge session tokens to its logs, and they are stored in Loki | LAB-STANDARDS §9 | chief | M | remediate | No | verified | new |
| MON-01 | medium | A dead container or a broken TV pairing raises no alert | LAB-STANDARDS §7 | chief | M | remediate | Yes | verified | new |
| BAK-01 | medium | The pairing config has one copy (the PBS guest backup) and no protect-list line | LAB-DEC-088 | jason | S | none | Yes | verified | needs Jason |
| HLT-01 | medium | No 'Connected to device' line for 6 days; cannot tell an idle TV from a lost pairing | SERVICE-REVIEW G | jason | S | none | Yes | unverified | needs Jason |
| DOC-03 | medium | Readiness is recorded as R1 'deliberate'; the live checks support R0 | Gate 2 | chief | S | none | No | verified | fixed (verified) |
| PAT-01 | low | No upgrade-backlog row and no release watcher for this image | LAB-STANDARDS §12 | chief | S | none | No | verified | fixed (verified) |
| SEC-03 | low | The container runs with default privileges: root user, writable filesystem, all capabilities, no limits | LAB-STANDARDS §9 | chief | S | remediate | No | unverified | fixed (verified) 2026-11-02 |
| SPD-01 | low | No measured baseline, and Prometheus has no CPU or memory series for the container | Gate 8 | chief | S | none | No | verified | still open |
| INT-01 | low | Nothing in the lab reads this container's skip activity, though it is easy to count | none | chief | M | none | No | verified | new |
| MON-02 | low | The SRE good-check has no stored reading, and today it would fail | SERVICE-REVIEW E | chief | S | none | No | verified | fixed (verified) |
| BAK-02 | low | No restore test covers this container's config | Gate 7 | chief | M | remediate | No | verified | needs PLAT |
| HLT-02 | low | Occasional connection errors and no health check or auto-restart for a stuck loop | SERVICE-REVIEW G | chief | S | remediate | No | verified | still open |
| DOC-01 | low | ARCHITECTURE, BASELINES and CHANGELOG are behind the live container | SERVICE-REVIEW H | chief | S | none | No | verified | still open |
| DOC-02 | low | Doc edits are not committed, and the lab-standards sync receipt is behind | SERVICE-REVIEW H | chief | S | none | No | verified | still open |
| DOC-04 | low | The preflight and the run plan say 'no repo for this slug' although the repo exists | SERVICE-REVIEW H | chief | S | none | No | verified | still open |
| USE-01 | low | A few settings are not used; none is clearly worth turning on | SERVICE-REVIEW I | chief | S | none | No | unverified | new |
| CC-01 | low | Command Center cannot show this container: its metrics carry the docker-host label, not the slug | SERVICE-REVIEW J | chief | M | none | No | unverified | new |

## PAT

### PAT-01: No upgrade-backlog row and no release watcher for this image

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** LAB-STANDARDS §12 · **State:** fixed (verified)
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** Section 12 row added in branch s0/FAM (running v2.11.0, upstream v2.11.0, tag pin, GitHub releases, checked 2026-11-02). Live: docker ps shows ghcr.io/dmunozv04/isponsorblocktv:v2.11.0. Adding the image to a version watcher for CT 108 is not done (no watcher exists in the docs); that part is carried by the lab-wide upgrade-check work.
- **Checked:** verified
- **Evidence:** Running v2.11.0 equals upstream latest v2.11.0 (GitHub releases API: v2.11.0 2026-08-21, v2.10.0 2026-07-13, v2.9.0 2026-05-09), so there is no gap today. grep for isponsorblocktv in LAB-STANDARDS.md finds only lines 1465 and 2016, no section 12 row. Compose pins the tag, not the digest (docker-compose.yml:7); UPSTREAM.txt holds the digest and its first 12 characters match the live image id d1e876636754. Releases came every 1 to 2 months in 2026. No What's Up Docker result for CT 108 was found.
- **Fix:** In a fix run, add a section 12 upgrade backlog row (running v2.11.0, upstream v2.11.0, tag pin, source GitHub releases, checked 2026-11-02) and add this image to whatever version watch covers CT 108. Optionally pin tag plus digest in compose. Undo: git revert.
- **Links:** LAB-DEC-022

## SEC

### SEC-01: config.json holds the TV pairing credentials and is readable by every user on CT 108

- **Severity:** medium · **Effort:** S · **Approval:** remediate · **Owner:** jason · **Standard:** LAB-STANDARDS §9 · **State:** fixed (verified) 2026-11-02
- **Checked:** verified
- **Evidence:** stat -c '%a %U:%G' /opt/isponsorblocktv/data/config.json -> 644 root:root, 717 bytes; folder drwxr-xr-x root. ARCHITECTURE section 13 says the file 'can remote-control the Living Room TV and the Office TV' and 'mode whatever the root-owned file was written as'. Contents were not read. The host review notes an agentread user exists on CT 108 (purpose unverified). The data folder is gitignored (.gitignore:7, git check-ignore confirms).
- **Fix:** In a fix run: chmod 600 /opt/isponsorblocktv/data/config.json and chmod 700 /opt/isponsorblocktv/data. The container runs as the default user (root) and reads the file as root, so no restart is expected; confirm with one 'Refreshed auth' line afterwards. Undo: chmod 644 / 755.
- **Links:** WISH-128, WISH-148
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** Ran `chmod 600 /opt/isponsorblocktv/data/config.json` and `chmod 700 /opt/isponsorblocktv/data` on CT 108 (about 23:38 Pacific). Verified by behaviour: the container (root) still reads the file (`wc -c` gives 717 bytes through `docker exec`) and the process kept running. The daily 'Refreshed auth' line is due at 20:25 tonight; check that it still appears. Undo: `chmod 644` on the file and `chmod 755` on the folder.

### SEC-02: The container writes lounge session tokens to its logs, and they are stored in Loki

- **Severity:** medium · **Effort:** M · **Approval:** remediate · **Owner:** chief · **Standard:** LAB-STANDARDS §9 · **State:** new
- **Checked:** verified
- **Evidence:** ARCHITECTURE section 13 already says 'Logs print lounge tokens'. Live: Loki count_over_time({container="isponsorblocktv"} |= "lounge id token") is 2 in 24 h and 33 in 30 d, so each daily 'Refreshed auth' line (one per paired device) is kept in Loki, which anyone with Loki read access can query. labread's own mask did not catch the 'lounge id token <value>' form, and two token values reached this lane's output (see incident_notes). The tokens refresh daily; whether an old token still controls a TV was not tested.
- **Fix:** In the Alloy config on CT 108 add a loki.process stage.replace that rewrites 'lounge id token <value>' to '<masked>' before it ships (Alloy lives in the docker-host lane; needs an Alloy restart, after hours). Add the same pattern to labread's mask list. Treat the stored lines as exposed to anyone with Loki read: tell Jason, and if a rotation is wanted, re-pairing both TVs issues new credentials. Undo: revert the Alloy config.
- **Links:** WISH-174, WISH-355

### SEC-03: The container runs with default privileges: root user, writable filesystem, all capabilities, no limits

- **Severity:** low · **Effort:** S · **Approval:** remediate · **Owner:** chief · **Standard:** LAB-STANDARDS §9 · **State:** fixed (verified) 2026-11-02
- **Checked:** unverified (labread refused the docker inspect fields for CapDrop, ReadonlyRootfs, SecurityOpt and limits, and refused docker exec id; the compose file shows none are set but the runtime values were not read.)
- **Evidence:** docker-compose.yml (12 lines) sets only image, container_name, restart and one volume: no user, read_only, cap_drop, security_opt no-new-privileges, mem_limit or pids_limit. docker inspect shows Config.User empty (default user), Privileged false, no socket mount, bridge network, no published port, RestartCount 0. A direct read of ReadonlyRootfs, CapDrop and limits was refused by labread (docker inspect fields beyond the allowed set), so the compose file is the evidence. Exposure is low: no listener, outbound only.
- **Fix:** In a fix run test and add cap_drop: [ALL], security_opt: [no-new-privileges:true], read_only: true with a tmpfs if needed, mem_limit 256m and pids_limit 100, then recreate the container (a brief break in skipping). Undo: restore the .bak compose file and recreate.
- **Links:** WISH-326
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** Replaced `/opt/isponsorblocktv/docker-compose.yml` (backup `docker-compose.yml.bak-s1-20261102`) with the same file plus `cap_drop: [ALL]`, `security_opt: [no-new-privileges:true]`, `read_only: true`, `tmpfs: [/tmp]`, `mem_limit: 256m`, `pids_limit: 100`, and ran `docker compose up -d` (CT 108, about 23:41 Pacific). First try worked. Verified: `docker inspect` shows ReadonlyRootfs=true, CapDrop=[ALL], no-new-privileges, 256 MiB, 100 pids, RestartCount 0, state running, and both TV clients logged 'Refreshed auth' on start with no errors. Memory use is 50 MiB of 256. Undo: copy the backup compose file back and run `docker compose up -d`. Note: the recreate also forced a fresh auth refresh; see the output note in the final report.

## SPD

### SPD-01: No measured baseline, and Prometheus has no CPU or memory series for the container

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** Gate 8 · **State:** still open
- **Checked:** verified
- **Evidence:** BASELINES.md holds one memory sample (47.77 MiB on 2026-10-08) and one CPU sample (0.00%), thresholds 'not yet measured', last_verified 2026-10-08. Live docker stats --no-stream: 15.75 MiB of 4 GiB, 0.00% CPU, 2 PIDs. The Prometheus metric list has docker_container_info but no per-container CPU or memory series, so no 7-day range can be built. The container is tiny, so sizing is fine.
- **Fix:** Record the new reading and a range in BASELINES.md after the host's cadvisor-style metrics exist (or take three docker stats samples over a week). Set a warning of 100 MiB as a starting guess, marked as a guess. Undo: git revert.
- **Links:** LAB-DEC-087
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** Recorded one new reading in BASELINES.md (50.35 MiB of the new 256 MiB limit, 0.00% CPU, 2 PIDs, right after the recreate) and a starting warning guess of 100 MiB, marked as a guess. The finding needs a range (three samples over a week), so it stays open for the follow-up run.

## INT

### INT-01: Nothing in the lab reads this container's skip activity, though it is easy to count

- **Severity:** low · **Effort:** M · **Approval:** none · **Owner:** chief · **Standard:** none · **State:** new
- **Checked:** verified
- **Evidence:** Docker logs 2026-10-08 to 2026-11-01: 334 videos played, 118 with 0 segments, 11 skips, last skip 2026-10-17. ARCHITECTURE section 6 describes the log lines. KarakeepErrorBurst and similar rules show Alloy loki.process counters feeding Prometheus already work in this lab (rules list). skip_count_tracking is true, so counts go to SponsorBlock but nothing local keeps them.
- **Fix:** Plan with MON-01: Alloy loki.process counters for 'Skipping segment', 'Playing video' and 'Connected to device' feed Prometheus; Command Center shows 'last video seen' and 'skips this month' on the Services page. No MCP and no new service. Undo: remove the Alloy stage.
- **Links:** WISH-316

## MON

### MON-01: A dead container or a broken TV pairing raises no alert

- **Severity:** medium · **Effort:** M · **Approval:** remediate · **Owner:** chief · **Standard:** LAB-STANDARDS §7 · **State:** new
- **Checked:** verified
- **Evidence:** Prometheus rules list (about 90 alerting rules): none uses docker_container_*, none uses the container's logs. No monitor_status series for the slug. Kuma monitors are not wanted by decision (SERVICE-INVENTORY.md:75, LAB-STANDARDS.md:2016, ARCHITECTURE section 16). docker-host MON-01 already notes a stopped container raises no alert. The pairing can fail without the container stopping: ARCHITECTURE section 14 says YouTube revokes screen ids, and the process stays Up. The container writes a 'Refreshed auth' line for each device every day at about 20:25 UTC (58 lines from 2026-10-08 to 2026-11-01, 2 per day), which is a ready-made heartbeat. A failure would be noticed only when Jason sees an unskipped sponsor.
- **Fix:** Alloy loki.process counter on 'Refreshed auth' for this container, label slug=isponsorblocktv, and a Prometheus rule: no increase in 26 h goes through Alertmanager to #alerts (LAB-DEC-061). Plus docker-host's container-state rule once it exists. Test with one Discord down and up with Jason's OK. Undo: remove the stage and the rule.
- **Your call:** Jason decided no DNS, NPM, Kuma, Homarr or UniFi work for this service (R1 deliberate). Is a log-derived Prometheus alert to #alerts acceptable under that decision? It adds none of those.
- **Links:** WISH-136, LAB-DEC-061, WISH-316

### MON-02: The SRE good-check has no stored reading, and today it would fail

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW E · **State:** fixed (verified)
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** SRE-AGENT-CATALOG good-check now uses the daily 'Refreshed auth' line (two devices within 26 h) and says a quiet 'Connected to device' spell is normal (branch s0/FAM). Live 2026-11-02: 2 'Refreshed auth' lines in the last 26 h. A stored reading is still not recorded; HLT-01 is unanswered.
- **Checked:** verified
- **Evidence:** SRE-AGENT-CATALOG.md:182: good-check is 'container is running and a recent log line says Connected to device'; 'Reading not stored. No health URL. watch: no'. Live: the container is running, but the last 'Connected to device' line is 2026-10-26 20:21 UTC, 6 days ago (see HLT-01). The check has no definition of 'recent'.
- **Fix:** Change the check to the daily 'Refreshed auth' line (two devices, within 26 h) and store one reading after HLT-01 is answered. Doc change in the catalog. Undo: git revert.

## BAK

### BAK-01: The pairing config has one copy (the PBS guest backup) and no protect-list line

- **Severity:** medium · **Effort:** S · **Approval:** none · **Owner:** jason · **Standard:** LAB-DEC-088 · **State:** needs Jason
- **Checked:** verified
- **Evidence:** /opt/isponsorblocktv/data/config.json (717 bytes, 2026-10-09) holds the screen ids for both TVs and the skip categories. ARCHITECTURE sections 7 and 8: not in git, 'not in Dashlane', Dashlane copy 'not done' (section 16), Kopia 'no additions'. LAB-STANDARDS section 6 has no isponsorblocktv line or 'no additions' entry. No Kopia source for the slug. PBS holds the whole guest nightly (newest 2026-11-01). If the file is lost, both TVs must be paired again with new codes, which needs Jason at each TV; the categories (3 of 10) would have to be re-entered from the runbook. The image comes from ghcr.io and re-pulls, so the docker-host BAK-01 image gap does not apply here.
- **Fix:** Decide. Cheapest: write 'isponsorblocktv: no additions, PBS only; loss means re-pairing both TVs' in LAB-STANDARDS section 6 and the runbook. If a copy is wanted, add /opt/isponsorblocktv/data/config.json to the docker-host Kopia list with the SEC-01 mode, or have Jason copy it by hand to the Dashlane path. Add a freshness monitor if Kopia is used (LAB-DEC-053). Undo: remove the source.
- **Your call:** Is PBS alone enough (re-pair both TVs if lost), or do you want a Dashlane or Kopia copy of config.json?
- **Links:** LAB-DEC-088, LAB-DEC-053, WISH-301
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** A choice for Jason. Option A (my recommendation): PBS only, write 'isponsorblocktv: no additions, PBS only; loss means re-pairing both TVs' in LAB-STANDARDS section 6 and the runbook. Option B: add `/opt/isponsorblocktv/data/config.json` (mode 600 now) to the docker-host Kopia list, which is lane PLAT's list, plus a freshness monitor. Option C: copy it by hand to a Dashlane note. The file is 717 bytes and the pairing codes are the only thing in it that cannot be rebuilt without Jason at both TVs.

### BAK-02: No restore test covers this container's config

- **Severity:** low · **Effort:** M · **Approval:** remediate · **Owner:** chief · **Standard:** Gate 7 · **State:** needs PLAT
- **Checked:** verified
- **Evidence:** restore-log.jsonl:31 holds the only CT 108 test (2026-10-07, pass). The container was installed 2026-10-08, after it. ARCHITECTURE section 10: 'Gate 7 for isponsorblocktv is not tested'. WISH-020 names iSponsorBlockTV among services with no restore test.
- **Fix:** Fold into the docker-host restore test (docker-host BAK-01): after restoring a recent CT 108 backup to a scratch CT with the network card down, check that /opt/isponsorblocktv/data/config.json is there. Do not start the container on the scratch CT with the network up: it would use the live TV credentials. Needs Jason's OK. Undo: destroy the scratch CT.
- **Links:** WISH-020
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** The finding folds into the docker-host scratch restore of CT 108, which lane PLAT owns. Request in `parts/FAM-requests.md`: when PLAT restores CT 108, check that `/opt/isponsorblocktv/data/config.json` exists and is mode 600, with the network removed and the container not started. CT 108 has a 24G disk, so the test is small.

## HLT

### HLT-01: No 'Connected to device' line for 6 days; cannot tell an idle TV from a lost pairing

- **Severity:** medium · **Effort:** S · **Approval:** none · **Owner:** jason · **Standard:** SERVICE-REVIEW G · **State:** needs Jason
- **Jason decision 2026-11-02:** not re-pairing the TVs now. Accepted risk, INC-20261102-03. Sprint 0 did not touch this item.
- **Checked:** unverified (The logs cannot say whether either TV played YouTube since 2026-10-26, and testing a skip needs a person at a TV.)
- **Evidence:** Docker logs counts (laptop grep, lines not read): last 'Connected to device' and last 'Subscribe completed' 2026-10-26 20:21:58; the last ERROR 2026-10-26 20:25:58 was a 'Handle subscribe payload error' (aiohttp TransferEncodingError); last 'Playing video' 2026-10-25 00:14; last 'Skipping segment' 2026-10-17. After the daemon restart on 2026-10-28 20:25 no new Connected line appeared in 4 days. Both devices still logged 'Refreshed auth' every day through 2026-11-01 20:25, so the process and the credentials are alive. Earlier quiet spells exist (no Connected 2026-10-12 to 2026-10-16, and 2026-10-20 to 2026-10-24) and then returned, so a quiet week can be normal when the TVs are not used.
- **Fix:** Jason plays one YouTube video with a known sponsor on each TV and reports. If skips fail, restart the container first (docker restart, remediate) and re-pair with a new TV code if that does not help (docs/RUNBOOK.md). Undo: none needed.
- **Your call:** Have the Living Room TV or the Office TV played YouTube since 2026-10-26, and did sponsors skip? If yes, the pairing is broken or the subscribe loop died after the 2026-10-26 error.
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** Unchanged and still Jason's: play one YouTube video with a known sponsor on each TV and report whether sponsors skip. No new 'Connected to device' line has appeared since the recreate either (the TVs are idle at night), so the logs still cannot answer it. Jason accepted the re-pair risk (INC-20261102-03).

### HLT-02: Occasional connection errors and no health check or auto-restart for a stuck loop

- **Severity:** low · **Effort:** S · **Approval:** remediate · **Owner:** chief · **Standard:** SERVICE-REVIEW G · **State:** still open
- **Checked:** verified
- **Evidence:** Docker logs 2026-10-08 to 2026-11-01: 8 ERROR lines (aiohttp ClientPayloadError, 'Handle subscribe payload error', on 2026-10-10 x2, 2026-10-18 x4, 2026-10-19, 2026-10-26), 16 traceback lines, 37 WARNING lines ('Unknown state' from pyytlounge, noted as harmless in TROUBLESHOOTING.md). Earlier errors were followed by new Connected lines; the 2026-10-26 one was the last event before the quiet period (HLT-01). docker ps shows 'Up 4 days' with no (healthy) flag; the compose file has no healthcheck. RestartCount 0.
- **Fix:** After HLT-01, add a compose healthcheck that fails when no log or heartbeat file has changed in 26 h (or accept the Prometheus alert in MON-01 instead). Recreate needs a short break in skipping. Undo: revert the compose file.
- **Links:** WISH-326
- **Fix run 2026-11-02 (lane FAM, Sprint 1):** The finding says to add the healthcheck after HLT-01 is answered. Nothing changed. A container healthcheck cannot read the Docker log, so the 26 hour rule would need a heartbeat file written by the app, which it does not write; the better answer may be the MON-01 Prometheus alert instead. Decide after HLT-01.

## DOC

### DOC-01: ARCHITECTURE, BASELINES and CHANGELOG are behind the live container

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW H · **State:** still open
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** Done in the clean files: BASELINES (memory 15.79 MiB, swap 122 of 512 MiB live, last_verified) and TROUBLESHOOTING (quiet-spell note, the 2026-10-26 subscribe error). Not done: ARCHITECTURE, CHANGELOG, README, CURSOR and RUNBOOK all hold another session's uncommitted edits (rule 7), so the restart note, the config.json mode 644 and the CHANGELOG line were not written. Live: config.json is 644 root, container up since the 2026-10-28 daemon restart.
- **Checked:** verified
- **Evidence:** Live: Up 4 days since a daemon restart on 2026-10-28 20:25 UTC (not in CHANGELOG); memory 15.75 MiB against 47.77 MiB in BASELINES.md; swap on CT 108 is 111 of 512 MiB, but BASELINES.md and ARCHITECTURE section 14 say 511 of 512 (docker-host HLT-03); config.json mode is 644, ARCHITECTURE section 13 says 'whatever the root-owned file was written as'; Connected/skip gaps (HLT-01) not recorded; the 2026-10-26 subscribe error is not in TROUBLESHOOTING.md. ARCHITECTURE last_verified 2026-10-08.
- **Fix:** Update the figures, mode, and restart note in ARCHITECTURE, BASELINES, TROUBLESHOOTING and CHANGELOG; refresh last_verified on each doc the review confirmed. Undo: git revert.

### DOC-02: Doc edits are not committed, and the lab-standards sync receipt is behind

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW H · **State:** still open
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** Skipped by rule 7: the seven modified files in the working tree belong to another session and were not reviewed, staged or committed, and the sync receipt in CLAUDE.md was not touched. Jason or that session must commit or discard them first.
- **Checked:** verified
- **Evidence:** git -C isponsorblocktv status --short: 7 modified files (ARCHITECTURE.md, CHANGELOG.md, CLAUDE.md, CURSOR.md, README.md, docs/RUNBOOK.md, .cursor/rules/lab-doc-contract.mdc) and 1 new file (.cursor/rules/decisions-with-docs.mdc). They hold the second-TV update of 2026-10-09 and the RAM note. HEAD ceaf4e1 (2026-10-29) has nothing unpushed. CLAUDE.md:30-37 names lab-standards 575bf04 from 2026-10-08; lab-standards is at c0c6b8c. validate-docs.py reports PASS.
- **Fix:** In a fix run, review and commit the working-tree edits by file name, refresh the sync receipt, push the repo first and lab-standards second. Undo: git revert.

### DOC-03: Readiness is recorded as R1 'deliberate'; the live checks support R0

- **Severity:** medium · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** Gate 2 · **State:** fixed (verified)
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** Recorded level is now R0 in the inventory rows, SERVICE-READINESS and LAB-STANDARDS (the level the review proposes, approved in HLT-04), with the reason: Gate 2 fails on CT 108 (docker-host SEC-01). R1 returns when that closes or Gate 2 is waived. The ARCHITECTURE header still says R1 (dirty file, skipped). The note that R2 and R3 are unreachable for a no-port service was added to the readiness row.
- **Checked:** verified
- **Evidence:** SERVICE-INVENTORY.md:75, SERVICE-READINESS.md:209, ARCHITECTURE.md header and LAB-STANDARDS.md:2016 say R1. R1 needs gates 1 and 2. Gate 2 fails on CT 108 (docker-host SEC-01: no jason user, no Host alias, lab key refused). The container itself works and the gap belongs to the host, but the cumulative rule gives R0. See the readiness conflict.
- **Fix:** Close docker-host SEC-01 (then R1 holds), or lower the recorded level to R0 until it is closed. Also state in the readiness docs that R2 and R3 are not reachable for a no-port service, but a log-based alert (MON-01) is the lab's way to notice failures. Undo: git revert.
- **Links:** Gate 2

### DOC-04: The preflight and the run plan say 'no repo for this slug' although the repo exists

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW H · **State:** still open
- **Fix run 2026-11-02 (Sprint 0, lane FAM):** The fix is a change to review-preflight.py in lab-standards, which is not this lane's file. Listed in FAM-ls-text.md for the lab-standards owner.
- **Checked:** verified
- **Evidence:** Evidence file preflight row 'Repo, HEAD, doc last_verified ages': 'no repo for this slug; results folder lab-standards/service-reviews/isponsorblocktv/'. The repo isponsorblocktv exists (Gitea jason/iSponsorBlockTV, HEAD ceaf4e1) and run.json uses results_dir isponsorblocktv/docs/reviews. The preflight finds repos by slug and cannot map this one because the Gitea and GitHub names keep the upstream capitalisation (SERVICE-SLUG-REGISTRY.md:255).
- **Fix:** Teach review-preflight.py to use the registry's repo name column or the SERVICE-SLUG-REGISTRY.md:255 exception. Doc and script change. Undo: git revert.

## USE

### USE-01: A few settings are not used; none is clearly worth turning on

- **Severity:** low · **Effort:** S · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW I · **State:** new
- **Checked:** unverified (Upstream documentation for v2.11.0 was not read in this lane; the list is from the lab runbook.)
- **Evidence:** The setup wizard (docs/RUNBOOK.md) lists: proxy, devices, YouTube API key, categories, channel whitelist, minimum skip length, report skips, mute ads, skip ads, autoplay, SponsorBlock URL. Used: devices (2), categories (3 of 10: sponsor, selfpromo, interaction), mute ads, skip ads, autoplay, report skips. Not used: channel whitelist (needs a YouTube Data API key, none issued), minimum skip length, proxy, and the other categories (intro, outro, preview, hook, exclusive_access, filler, music_offtopic), off on purpose per ARCHITECTURE section 16. The feature list comes from the lab's runbook; the upstream docs were not read, so extra features may exist.
- **Fix:** No change. If Jason wants it, 'intro' and 'outro' categories are the likeliest next step for family viewing; edit config.json and restart. Undo: restore the previous categories.

## CC

### CC-01: Command Center cannot show this container: its metrics carry the docker-host label, not the slug

- **Severity:** low · **Effort:** M · **Approval:** none · **Owner:** chief · **Standard:** SERVICE-REVIEW J · **State:** new
- **Checked:** unverified (The Command Center Services page was not opened; only the metric labels and TOOLS.yaml were read.)
- **Evidence:** Preflight: 'Prometheus ok, no service or host label found' for the slug. Live: docker_container_info{container="isponsorblocktv"} has service=docker-host, host=docker, compose_project=isponsorblocktv. The Services row comes from TOOLS.yaml (what, lab_use present) and has an icon (serviceIcons.ts:87), but there is no health, last-activity or link on it. The Command Center page itself was not read.
- **Fix:** Join on container name or compose_project to the slug in Command Center, and show state, last 'Refreshed auth' time, and last video seen once INT-01 exists. Link to Portainer's container page. Undo: remove the panel.
- **Links:** WISH-316
