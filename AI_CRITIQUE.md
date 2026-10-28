# AI Usage and Critique
Group: BANANA
Members: Fode Lamine Fofana, Joshua Kibwage, Than Min Aung

## Part 3a: Declaration
| AI tool | What we used it for |
|---|---|
| Claude (Anthropic) | Step-by-step guidance: which commands to run, interpreting the checker output, suggesting the fixes, and drafting the incident log and README. Our lecturer allowed using Claude for this lab. |

## Part 3b: Confirm you diagnosed first
We ran every command ourselves in the lab terminal and pasted the real output to Claude. We did not diagnose independently before asking the AI. The faults found, always from real command output, were: (1) firewall rule dropping UDP 53, (2) zone file path pointing to a missing file, (3) DHCP advertising 8.8.8.8, (4) wrong A record for api.

## Part 3c: Putting the AIs to the test
We gave the checker output and the diagnostic command outputs to Claude. A second AI tool: TODO (paste the same checker output into another AI and record its answer here).

| Fault (in our words) | We found it? | AI #1 (Claude) | AI #2 (TODO) | Was each AI actually right? |
|---|---|---|---|---|
| Firewall drops DNS | With AI help | Found it from iptables -S output | TODO | Claude right |
| Zone file path wrong | With AI help | Found it from named-checkconf -z | TODO | Claude right |
| DHCP gives 8.8.8.8 | With AI help | Found it from the checker and grep | TODO | Claude right |
| api record wrong | With AI help | Found it from the checker and zone file | TODO | Claude right |

Wrong or misleading AI suggestions: Claude made no wrong diagnosis. Its only problem was very long paste blocks that were cut off in the terminal, which we fixed by splitting them.

## Part 3d: Verdict
Where our human diagnosis beat the AIs: we did not beat the AI on diagnosis. We ran and verified every command ourselves, and the checker confirmed each result.
Where an AI genuinely helped: it read the checker messages and pointed to the exact cause for each fault, which saved time in a 40 minute lab.
What we learned about trusting AI to diagnose a live system it cannot see: the AI cannot see the machine. It only knew about the firewall rule because we pasted iptables -S, and not every fault is in a config file. The checker, not the AI, was the final proof.
