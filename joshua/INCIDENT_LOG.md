# Incident Log

Group: BANANA   Members: Fode Lamine Fofana, Joshua Kibwage
Company / domain: novapeak.lab

---

## Fault 1

- **Symptom (what we observed):**
```
  python3 /root/grading/check_lab.py --group lab01 --phase build
  [ 0/2 ] C1 client cannot reach named at 192.168.19.10 (timeout/no status). Check listen-on, allow-query, firewall...
```
- **Hypothesis (what we suspected and why):**
  The DNS server was timing out, so I suspected a firewall rule blocking DNS traffic on port 53.
- **Proof (the command that confirmed the cause):**
```
  iptables -S INPUT | grep 53
  -A INPUT -p udp -m udp --dport 53 -j DROP
```
- **Fix (what we changed):**
  Deleted the rule that drops UDP port 53:
  iptables -D INPUT -p udp -m udp --dport 53 -j DROP
- **Confirmation (output showing it now works):**
```
  iptables -S INPUT | grep 53
  (no output, the DROP rule is gone)
  Final checker: [ 2/2 ] C1 named is authoritative for novapeak.lab and reachable from the client.
```
- **Found/proved by:** Joshua Kibwage

---

## Fault 2

- **Symptom (what we observed):**
```
  [ 1/2 ] C4 named-checkconf -z FAILED; named-checkzone clean
```
- **Hypothesis (what we suspected and why):**
  named-checkconf failed, so I suspected a wrong file path for the novapeak.lab zone in the BIND config.
- **Proof (the command that confirmed the cause):**
```
  named-checkconf -z
  zone novapeak.lab/IN: loading from master file /etc/bind/db.MISSING failed: file not found
  grep -n file /etc/bind/named.conf.local
  3:      file "/etc/bind/db.MISSING";
```
- **Fix (what we changed):**
  Changed the zone file path in /etc/bind/named.conf.local from db.MISSING to /etc/bind/db.novapeak.lab, then restarted named.
- **Confirmation (output showing it now works):**
```
  3:      file "/etc/bind/db.novapeak.lab";
  zone novapeak.lab/IN: loaded serial 2024010101
  Final checker: [ 2/2 ] C4 named-checkconf -z clean; named-checkzone clean
```
- **Found/proved by:** Joshua Kibwage

---

## Fault 3

- **Symptom (what we observed):**
```
  [ 0/3 ] C9 DHCP advertises 8.8.8.8, expected the company DNS 192.168.19.10
  [ 0/3 ] C11 using the leased resolver 8.8.8.8, www did not resolve to 192.168.19.20
```
- **Hypothesis (what we suspected and why):**
  Clients were being given a public DNS server instead of our own, so I suspected the DHCP DNS option was wrong.
- **Proof (the command that confirmed the cause):**
```
  grep -n domain-name-servers /etc/dhcp/dhcpd.conf
  8:      option domain-name-servers 8.8.8.8;
```
- **Fix (what we changed):**
  Changed 8.8.8.8 to 192.168.19.10 in /etc/dhcp/dhcpd.conf, tested the config with dhcpd -t, then restarted isc-dhcp-server.
- **Confirmation (output showing it now works):**
```
  grep -n domain-name-servers /etc/dhcp/dhcpd.conf
  8:      option domain-name-servers 192.168.19.10;
  Final checker: [ 3/3 ] C9 DHCP advertises the company DNS (192.168.19.10)
  Final checker: [ 3/3 ] C11 client resolves www via the leased resolver
```
- **Found/proved by:** Joshua Kibwage

---

## Fault 4

- **Symptom (what we observed):**
```
  [ 0.0/3 ] C2 ... api expected 192.168.19.40 got nothing
```
- **Hypothesis (what we suspected and why):**
  The checker expects api at 192.168.19.40, so I suspected the api A record in the zone file had a wrong IP address.
- **Proof (the command that confirmed the cause):**
```
  grep -n -i -E 'api|serial' /etc/bind/db.novapeak.lab
  7:api IN A 192.168.19.1
```
- **Fix (what we changed):**
  Changed the api A record from 192.168.19.1 to 192.168.19.40 and raised the SOA serial from 2024010101 to 2024010102 so the change is picked up. Then restarted named.
- **Confirmation (output showing it now works):**
```
  7:api IN A 192.168.19.40
  2:@ IN SOA ns1.novapeak.lab. admin.novapeak.lab. ( 2024010102 3600 1800 604800 3600 )
  named-checkzone novapeak.lab /etc/bind/db.novapeak.lab
  zone novapeak.lab/IN: loaded serial 2024010102
  OK
  Final checker: [ 3.0/3 ] C2 all four A records correct
  Final checker: TOTAL: 25.0 / 25
```
- **Found/proved by:** Joshua Kibwage
