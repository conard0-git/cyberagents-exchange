---
name: "Targeted Nessus Scan"
author: "conard0-git"
github_url: "https://github.com/conard0-git/targeted-nessus-scan"
description: "On-demand Nessus vuln and STIG scans of specific hosts, a combined Excel report, and agent-profile assignment."
license: "MIT"
tier: "contributed"
tags: ["nessus", "tenable", "stig", "compliance", "vulnerability-scanning", "aws", "ec2", "reporting"]
integrations: ["AWS", "Tenable"]
date_added: 2026-08-06
contribution_agreement_date: 2026-08-06T20:32:58Z
compatible_platforms: ["Claude Code"]
invocation: "/targeted-nessus-scan"
---

Launch ad-hoc, agent-based scans against a known set of hosts on an existing
Nessus Manager, aggregate the findings into one combined Excel workbook, and
manage agent-profile assignments — by explicit IP list or by matching cloud
(AWS EC2) instances on a tag. Built around temporary, self-cleaning scan
artifacts so an on-demand scan never leaves stray groups or state behind.

## What it does

- **Ad-hoc targeted scan.** Take an IP list and one or both scan types
  (vulnerability, STIG), resolve the linked agents on the chosen Nessus
  Manager, create a temporary agent group, launch the scan against a named
  policy, poll to completion, and export the `.nessus` result.
- **Combined Excel report.** Aggregate the findings into a single workbook:
  Dashboard tab, Vulnerability Findings tab, STIG Findings tab. Color-code
  rows by severity (Critical red, Medium orange, Low yellow, unknown gray)
  and by STIG CAT level; sort STIG rows CAT I → CAT II → CAT III;
  auto-filter, freeze headers, autosize columns.
- **Agent-profile assignment.** Two entry points: assign by explicit IP
  list, or match AWS EC2 instances by a tag substring and assign their
  Nessus agents to the target profile (looked up by UUID or name).
  Cloud-tag flow requires a dry-run preview showing the matched instances
  and their Nessus linkage before anything mutates state.
- **Automatic cleanup.** Temporary scan groups are removed after the run
  unless the caller explicitly opts out; raw `.nessus` files are kept only
  on explicit request.

## How it works

The skill separates *per-run* state (the temporary agent group used for the
targeted scan) from *long-lived* state (the agent profile a host stays
assigned to). Both are treated as intentional mutations of the Nessus
Manager and are never conflated. Configuration is entirely environment-
variable driven — Nessus URLs and API keys, policy names to reference,
optional AWS credentials for the cloud-tag flow — with no config files,
hardcoded secrets, account IDs, or policy IDs. See `SKILL.md` for the full
workflow, `references/scanning-and-reporting.md` for the scan+report
mechanics (including CAT-level detection), and
`references/agent-profile-assignment.md` for the profile-assignment flows.
