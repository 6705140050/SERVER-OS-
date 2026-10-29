# Incident Log

Group: BANANA   Members: Fode Lamine Fofana, Joshua Kibwage
Company / domain: novapeak.lab

---

## Fault 1

- **Symptom (what we observed):**
  ```
  python3 /root/grading/check_lab.py --group lab01 --phase build
  [ 0/2 ] C1 client cannot reach named at 192.168.19.10 (timeout/no status)
  ```
- **Hypothesis (what we suspected and why):**
  named listens on 192.168.19.10 but something blocks DNS traffic, so a firewall rule is likely.
- **Proof (the command that confirmed the cause):**
  ```
  iptables -S
  -A INPUT -p udp -m udp --dport 53 -j DROP
  ```
- **Fix (what we changed):**
  iptables -D INPUT -p udp -m udp --dport 53 -j DROP
- **Confirmation (output showing it now works):**
  ```
  iptables -S INPUT
  -P INPUT ACCEPT
  [ 2/2 ] C1 named is authoritative for novapeak.lab and reachable from the client.
  ```
- **Found/proved by:** Fode Lamine Fofana

---

## Fault 2

- **Symptom (what we observed):**
  ```
  named-checkconf -z
  zone novapeak.lab/IN: loading from master file /etc/bind/db.MISSING failed: file not found
  zone novapeak.lab/IN: not loaded due to errors.
  ```
- **Hypothesis (what we suspected and why):**
  The zone statement points to a wrong file name and the real zone file exists under another name.
- **Proof (the command that confirmed the cause):**
  ```
  ls -l /etc/bind/ (db.novapeak.lab exists, db.MISSING does not)
  grep -n -A6 novapeak /etc/bind/named.conf.local (file "/etc/bind/db.MISSING";)
  ```
- **Fix (what we changed):**
  Changed the file path in /etc/bind/named.conf.local to /etc/bind/db.novapeak.lab and restarted named.
- **Confirmation (output showing it now works):**
  ```
  named-checkconf -z
  zone novapeak.lab/IN: loaded serial 2024010101
  [ 2/2 ] C4 named-checkconf -z clean; named-checkzone clean
  ```
- **Found/proved by:** Fode Lamine Fofana

---

## Fault 3

- **Symptom (what we observed):**
  ```
  [ 0/3 ] C9 DHCP advertises 8.8.8.8, expected the company DNS 192.168.19.10
  [ 0/3 ] C11 using the leased resolver 8.8.8.8, www did not resolve to 192.168.19.20
  ```
- **Hypothesis (what we suspected and why):**
  dhcpd.conf hands out a public DNS server instead of the company DNS.
- **Proof (the command that confirmed the cause):**
  ```
  grep -n "domain-name-servers" /etc/dhcp/dhcpd.conf
  8:      option domain-name-servers 8.8.8.8;
  ```
- **Fix (what we changed):**
  Changed the option to 192.168.19.10 and restarted isc-dhcp-server.
- **Confirmation (output showing it now works):**
  ```
  [ 3/3 ] C9 DHCP advertises the company DNS (192.168.19.10)
  [ 3/3 ] C11 client resolves www via the leased resolver
  ```
- **Found/proved by:** Fode Lamine Fofana

---

## Fault 4

- **Symptom (what we observed):**
  ```
  [ 2.25/3 ] C2 api expected 192.168.19.40 got ['192.168.19.1']
  ```
- **Hypothesis (what we suspected and why):**
  The api A record in the forward zone has the wrong IP address.
- **Proof (the command that confirmed the cause):**
  ```
  cat /etc/bind/db.novapeak.lab
  api IN A 192.168.19.1
  ```
- **Fix (what we changed):**
  Changed the record to 192.168.19.40, raised the serial to 2024010102 and restarted named.
- **Confirmation (output showing it now works):**
  ```
  named-checkzone novapeak.lab /etc/bind/db.novapeak.lab
  zone novapeak.lab/IN: loaded serial 2024010102
  OK
  [ 3.0/3 ] C2 all four A records correct
  TOTAL: 25.0 / 25
  ```
- **Found/proved by:** Fode Lamine Fofana
