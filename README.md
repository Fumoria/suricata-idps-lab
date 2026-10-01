# Suricata IDS/IDPS Lab

## Environment

- Virtual machine: Kali GNU/Linux Rolling 2026.2
- Suricata: 8.0.7
- Network interface: eth0
- IP address: 10.0.2.15/24
- HOME_NET: 10.0.2.0/24

## 1. Suricata installation

Suricata and jq were installed using APT.

The installed Suricata version was verified with:

`suricata -V`

The Suricata service was started and verified as active.

## 2. Network configuration

The active network interface was identified as `eth0`.

The Suricata configuration file `/etc/suricata/suricata.yaml` was updated with:

`HOME_NET: "[10.0.2.0/24]"`

## 3. Rules update

Suricata rules were downloaded and updated using:

`sudo suricata-update`

The ruleset was successfully tested and loaded.

## 4. Suricata logs

The following logs were checked:

- `/var/log/suricata/suricata.log`
- `/var/log/suricata/stats.log`
- `/var/log/suricata/fast.log`
- `/var/log/suricata/eve.json`

EVE JSON alerts were parsed using `jq`.

## 5. Test alert

HTTP traffic was generated to verify Suricata detection.

Suricata successfully generated the alert:

`GPL ATTACK_RESPONSE id check returned root`

## 6. Local rules from the presentation

Two local rules were created:

### HTTP rule

`alert http any any -> any any (msg:"do not read gossip during work"; flow:to_client,established; classtype:policy-violation; sid:10001; rev:1;)`

### ICMP rule

`alert icmp any any -> any any (msg:"finally pinged"; sid:10002; rev:1;)`

Both rules were successfully triggered and recorded in `fast.log`.

## 7. Custom Suricata rules

Two additional custom rules were created.

### Custom rule 1 — outbound ICMP detection

`alert icmp $HOME_NET any -> $EXTERNAL_NET any (msg:"CUSTOM outbound ICMP detected"; itype:8; sid:10003; rev:1;)`

Testing:

`ping -c 1 8.8.8.8`

Result:

`CUSTOM outbound ICMP detected`

### Custom rule 2 — curl User-Agent detection

`alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"CUSTOM curl User-Agent detected"; flow:established,to_server; http.user_agent; content:"curl/"; nocase; sid:10004; rev:1;)`

Testing:

`curl http://testmyids.com/`

Result:

`CUSTOM curl User-Agent detected`

## 8. Configuration validation

The final configuration was checked using:

`sudo suricata -T -c /etc/suricata/suricata.yaml`

Result:

`Configuration provided was successfully loaded. Exiting.`

## Result

Suricata was successfully installed, configured and tested.

The standard examples from the presentation were reproduced, and two additional custom Suricata rules were created and successfully triggered.
