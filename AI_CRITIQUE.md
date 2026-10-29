# AI Usage and Critique
**Group:** BANANA  
**Members:** Fode Lamine Fofana, Joshua Kibwage

## Part 3a: Declaration

| AI tool | What we used it for |
|---|---|
| Claude (Anthropic) | Used for light clarification of command outputs, checking our explanations, and correcting the wording of the incident log and README. Our lecturer allowed using Claude for this lab. |

## Part 3b: Confirm you diagnosed first

We ran the diagnostic commands ourselves in the lab terminal and checked the actual output. We used AI as a supporting tool to help clarify technical messages and improve our written explanations. The faults we investigated were: (1) firewall rule dropping UDP 53, (2) zone file path pointing to a missing file, (3) DHCP advertising 8.8.8.8, and (4) an incorrect A record for api.

## Part 3c: Putting the AIs to the test

We compared the diagnostic results with suggestions from two AI tools: Claude and ChatGPT.

| Fault (in our words) | We found it? | AI #1 (Claude) | AI #2 (ChatGPT) | Was each AI actually right? |
|---|---|---|---|---|
| Firewall drops DNS | Yes, by checking command output | Helped explain the `iptables -S` output | Suggested allowing DNS if a firewall was enabled but did not identify the DROP rule | Claude was right; ChatGPT was incomplete. |
| Zone file path wrong | Yes, by checking command output | Helped interpret the `named-checkconf -z` output | Suggested checking zone file paths but did not identify `db.MISSING` | Claude was right; ChatGPT was partly right. |
| DHCP gives 8.8.8.8 | Yes, by checking the configuration | Helped clarify the configuration issue | Identified that the DNS option should be changed to `192.168.19.10` | Both were right. |
| API record wrong | Yes, by checking the checker output and zone file | Helped explain the issue in the zone file | Did not identify the incorrect API address or serial and provided a generic zone example | Claude was right; ChatGPT was incorrect or incomplete. |

**Wrong or misleading AI suggestions:** Claude did not provide an incorrect diagnosis. Some of its suggested text was too long to paste into the terminal at once, so we split it into smaller sections.

## Part 3d: Verdict

**Where our human diagnosis beat the AIs:** We used our own terminal commands and configuration checks to investigate the faults. We compared the AI suggestions against the actual results rather than accepting them without verification.

**Where an AI genuinely helped:** AI helped clarify technical messages and improve the wording of our documentation, making it easier to explain the problems and their solutions.

**What we learned about trusting AI to diagnose a live system it cannot see:** AI cannot directly inspect our lab machine and must rely on the information provided to it. Its suggestions are not always complete or accurate. We learned to check command outputs, verify configuration changes, and use the checker results as our final confirmation.
