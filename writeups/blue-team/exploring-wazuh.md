# Exploring Wazuh

| | |
|---|---|
| Platform | TryHackMe |
| Difficulty | Medium (60 min) |
| Category | Blue team |
| Link | [https://tryhackme.com/room/exploringwazuh?utm_campaign=social_share&utm_medium=social&utm_content=share-completed-room&utm_source=copy&sharerId=69c662cab3242a2f25a8b353 |

## Objective
Understand Wazuh as a unified open-source security platform (EDR/HIDS, SIEM, vulnerability and configuration auditing) and use its dashboard to monitor endpoints, investigate logs, and review alerts.

## Tools used
`Wazuh manager` `Wazuh dashboard (OpenSearch Dashboards)` `Wazuh agents on Windows Server 2019 and Ubuntu 24.04`

## Architecture in one minute
- **Manager and agents:** one Wazuh server processes data, many hosts (agents) send events to it
- **Components:** indexer (OpenSearch), manager (analysis engine), Filebeat (ships data to the indexer), dashboard (UI)
- In this lab everything runs on a single VM, with two agents connected (one Linux, one Windows)

## Approach
1. **Deployed the lab** and waited for the Wazuh services to start, then logged into the dashboard.
2. **Reviewed the agents:** checked the Agents summary (status, OS, groups) and learned what is needed to deploy a new agent (server IP, agent name, group).
3. **IT Hygiene (Windows agent):** used the System, Software, Network and Identity tabs to see hardware and OS details, installed applications, listening ports, and local users with their privileges.
4. **Configuration Assessment:** looked at the CIS Microsoft Windows Server 2019 Benchmark. Wazuh runs 347 checks (mostly PowerShell commands) and reports pass/fail per check, which helps find misconfigurations before an audit.
5. **Vulnerability Detection:** reviewed findings by severity on the Linux agent, then filtered the Inventory by package name. The scan produces over a thousand findings, but only a few are really exploitable, so they need prioritising by an administrator.
6. **Logging:** opened Discover, searched `decoder.name: sshd` over a wide time range, and read the SSH authentication events. Learned that these events are alerts created by a specific Wazuh rule.
7. **Dashboards:** built a pie chart on the `wazuh-alerts-*` index, split by `agent.name`, to see which host generates the most alerts.
8. **Agent configuration, decoders and rules:** reviewed how groups push log collection settings to agents (Linux auth log, Windows Defender and Sysmon logs), how decoders parse raw logs into fields, and how rules turn parsed events into alerts with a level from 1 to 15.
9. **Extra features:** read about alert notifications (query such as `rule.level >= 12` sent to Slack or email), Active Response, and File Integrity Monitoring.


## Defender's view
- **Visibility:** agents on every endpoint give a SOC one place to search logs from Windows and Linux hosts
- **Prioritisation:** rule levels let analysts focus on high-severity alerts first, and notifications on high levels reduce response time
- **Hardening:** CIS benchmark results and IT Hygiene data show weak settings, risky open ports and unnecessary software
- **Patch management:** vulnerability detection is noisy out of the box, so findings should be prioritised by severity and real exploitability
- **Tampering detection:** File Integrity Monitoring logs changes to sensitive files and registry keys
- **Limits:** Active Response runs with a delay, so it supports but does not replace a real-time EDR

## Key takeaways
- Logs are not alerts in Wazuh: decoders parse, rules decide what becomes an alert
- A SIEM is only as good as its collection settings and rules
- Raw vulnerability counts need context before they become a patching plan

## Skills
`SIEM` `Log analysis` `Alert triage` `Vulnerability management` `Configuration auditing` `Blue team`
