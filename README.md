
# 🛡️ Defensive Firewall Lab

> A layered `iptables` firewall for a **Lubuntu** VM simulating hardened Modbus and Redis industrial services.  
> Built for educational use in isolated lab environments.

![Platform](https://img.shields.io/badge/platform-Lubuntu%20%2F%20Ubuntu-E95420?style=flat-square&logo=ubuntu&logoColor=white)
![Language](https://img.shields.io/badge/firewall-iptables-00C8FF?style=flat-square)
![Python](https://img.shields.io/badge/honeypot-Python%203-3776AB?style=flat-square&logo=python&logoColor=white)
![License](https://img.shields.io/badge/use-educational%20only-FFB347?style=flat-square)

---

## ⚠️ Warning

> **Run this only on a machine and network you own or are authorized to administer.**  
> The default `INPUT` policy is `DROP`. A misconfigured `ADMIN_CIDR` **will** lock you out of SSH.  
> Always test on a disposable VM snapshot first. Do **not** run from an SSH session you cannot recover.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Defense Layers](#defense-layers)
- [Installation](#installation)
- [Configuration](#configuration)
  - [NAT Redirection — Layer 07](#nat-redirection--layer-07)
  - [Source-Port Condition — Layer 08](#source-port-condition--layer-08)
  - [Port Knocking — Layer 09](#port-knocking--layer-09)
  - [Hint Puzzle — Layer 12](#hint-puzzle--layer-12)
- [Honeypot Service](#honeypot-service)
- [Persistence](#persistence)
- [Verification](#verification)
- [Port Knocking Flow](#port-knocking-flow)
- [Limitations](#limitations)

---

## Overview

This lab deploys a **multi-layer defensive firewall** on a Lubuntu VM that exposes simulated **Modbus (TCP 502)** and **Redis (TCP 6379)** services — both hidden behind NAT redirection, port knocking, and source-port filtering. It is designed to demonstrate real-world defense-in-depth techniques against reconnaissance and lateral movement.

**Stack:**

| Component | Role |
|---|---|
| `iptables` / `ip6tables` | Packet filtering and NAT |
| `conntrack` | Stateful connection tracking |
| `hashlimit` / `recent` | Rate limiting and port knocking |
| `Python 3 asyncio` | Honeypot listener |
| `systemd` | Service hardening and persistence |

---

## Architecture

```
[External Scanner / Attacker VM]
           │
           ▼
   ┌───────────────────────┐
   │  LAB_IF  (iptables)   │  ◄── e.g. enp0s3
   └──────────┬────────────┘
              │  PREROUTING (NAT)
              │  :9000 → :502
              │  :9002 → :6379
              ▼
   ┌───────────────────────────────────────────┐
   │              INPUT CHAIN                  │
   │  00. Default DROP                         │
   │  02. conntrack (ESTABLISHED / INVALID)    │
   │  03. Bad TCP flags → DROP                 │
   │  04. ICMP rate limit                      │
   │  05. Trap ports 21, 23, 445, 3389         │
   │  06. SYN + Modbus rate limits             │
   │  08. Source-port check (sport 53)         │
   │  09. Port knocking → Redis access         │
   │  10. Direct port blocking                 │
   │  11. Decoy ports (open / RST / filtered)  │
   │  14. Catch-all logging                    │
   └────────────┬────────────────┬─────────────┘
                ▼                ▼
        [Modbus :502]       [Redis :6379]
        [Honeypot :9101–9104]
```

---

## Defense Layers

| Layer | Name | Description |
|------:|------|-------------|
| `00` | **Default Deny** | `DROP` all INPUT/FORWARD; loopback and established traffic allowed |
| `01` | Deep Packet Match | Optional literal `nmap` string match — disabled by default |
| `02` | **Stateful Filtering** | Accept `ESTABLISHED,RELATED`; drop `INVALID` |
| `03` | **Bad TCP Flags** | Drop NULL, Xmas, FIN-only, SYN+FIN, SYN+RST probes |
| `04` | **ICMP Control** | Rate-limit echo requests; allow essential ICMP error types |
| `05` | **Trap Ports** | Block source IPs touching ports 21, 23, 445, 3389 for 60 s |
| `06` | **Rate Limiting** | Limit new SYNs and new Modbus connections per source IP |
| `07` | **NAT Redirection** | Redirect `:9000` → `:502` and `:9002` → `:6379` in PREROUTING |
| `08` | **Source-Port Filter** | Modbus only allowed when client TCP source port is 53 |
| `09` | **Port Knocking** | Sequence 7331 → 4821 → 6150 authorizes Redis for 300 s |
| `10` | **Direct-Port Hiding** | Reject `:502` directly; silently drop `:6379` directly |
| `11` | **Deception / Noise** | 4 open decoys, 4 RST ports, 4 silently filtered ports |
| `12` | **Hint Puzzle** | Base64 knock-port fragments hidden in SSH/HTTP/FTP banners |
| `13` | **Honeypot Logging** | Python listener logs source address and received payload |
| `14` | **Firewall Logging** | Rate-limited `DEF-*` log prefixes via kernel |
| `15` | **OS Fingerprint Confusion** | Outgoing IPv4 TTL set to 128; TCP timestamps disabled |
| `16` | **Service Hardening** | `systemd` sandbox for honeypot — no root, strict filesystem |
| `17` | **Persistence** | Rules saved to `/etc/iptables/` and restored at boot |

---

## Installation

### 1. Prerequisites (Lubuntu / Ubuntu)

```bash
sudo apt update
sudo apt install -y iptables iptables-persistent python3
```

### 2. Identify your interface

```bash
ip -br address
ip route
```

> On Lubuntu, the lab interface is typically named `enp0s3` or `eth0` — do **not** guess. Confirm with the commands above.

### 3. Create and configure the firewall script

```bash
sudo install -d -m 0755 /usr/local/sbin
sudo nano /usr/local/sbin/defensive-firewall.sh
```

Paste the script content, then **edit the two variables at the top** before running:

```bash
LAB_IF="eth1"               # ← your actual lab interface — NOT the example value
ADMIN_CIDR="192.168.56.5/32" # ← your admin machine IP/CIDR
```

```bash
sudo chmod 0755 /usr/local/sbin/defensive-firewall.sh
sudo /usr/local/sbin/defensive-firewall.sh
```

> **Important:** The script flushes all existing iptables rules. The safety check at the top refuses to run if `LAB_IF` is still set to the placeholder value `enp0s8`.

---

## Configuration

### NAT Redirection — Layer 07

| External Port | Redirected To | Service |
|:---:|:---:|---|
| TCP 9000 | TCP 502 | Modbus simulator |
| TCP 9002 | TCP 6379 | Redis simulator |

The simulators must listen locally on `:502` and `:6379`. Confirm with:

```bash
sudo ss -lntup
```

---

### Source-Port Condition — Layer 08

Modbus (`:9000`) only accepts connections where the **client's TCP source port is 53**.

```bash
# Blocked — normal ephemeral source port
nmap -sT -p 9000 TARGET_IP

# Allowed — source port 53
nmap -sT -g 53 -p 9000 TARGET_IP
```

> Source port 53 is **not authentication** — any client can deliberately select it. This is a lab discovery condition only.

---

### Port Knocking — Layer 09

Send all three knocks from the **same source IP**, in order, within 10-second windows:

```bash
for port in 7331 4821 6150; do
  nc -z -w 1 TARGET_IP "$port"
done
```

This grants access to Redis (`:9002`) for **300 seconds**.

---

### Hint Puzzle — Layer 12

Generate Base64-encoded knock ports for embedding in service banners:

```bash
printf '%s' '7331' | base64   # → NzMzMQ==
printf '%s' '4821' | base64   # → NDgyMQ==
printf '%s' '6150' | base64   # → NjE1MA==
```

Place one fragment in each of the SSH / HTTP / FTP service banners.

> **Note:** Base64 is encoding, not encryption. Anyone who obtains the fragments can decode them immediately.

---

## Honeypot Service

A process must be listening for a port to appear open — firewall rules alone cannot create open TCP ports.

### Python Listener (Layers 11 & 13)

The honeypot listens on TCP **9101–9104**, logs the source address and received payload.

```bash
sudo install -d -o root -g root -m 0755 /opt/defensive-lab
sudo nano /opt/defensive-lab/honeypot.py
```

Create the log file and a restricted service account:

```bash
sudo useradd --system --no-create-home --shell /usr/bin/nologin svchoney
sudo install -o svchoney -g svchoney -m 0640 /dev/null /var/log/honeypot.log
sudo chown -R root:root /opt/defensive-lab
sudo chmod 0755 /opt/defensive-lab
sudo chmod 0644 /opt/defensive-lab/honeypot.py
```

### systemd Unit Hardening — Layer 16

Create `/etc/systemd/system/defensive-honeypot.service`:

```ini
[Unit]
Description=Defensive Lab Honeypot
After=network.target

[Service]
Type=simple
User=svchoney
Group=svchoney
ExecStart=/usr/bin/python3 /opt/defensive-lab/honeypot.py
Restart=on-failure
RestartSec=3
NoNewPrivileges=true
ProtectSystem=strict
ProtectHome=true
PrivateTmp=true
PrivateDevices=true
ProtectKernelTunables=true
ProtectKernelModules=true
ProtectControlGroups=true
RestrictSUIDSGID=true
LockPersonality=true
ReadWritePaths=/var/log/honeypot.log

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now defensive-honeypot.service
sudo systemctl status defensive-honeypot.service
```

---

## Persistence

### Save and Restore Rules — Layer 17

`iptables-persistent` (installed above) handles save and restore automatically on Lubuntu/Ubuntu.

```bash
# Save current rules after applying the firewall script
sudo netfilter-persistent save
```

Rules are restored automatically at boot via the `netfilter-persistent` service. After editing the script, reapply and save:

```bash
sudo /usr/local/sbin/defensive-firewall.sh
sudo netfilter-persistent save
```

Ensure the service is enabled:

```bash
sudo systemctl enable netfilter-persistent
```

### OS Fingerprint Confusion — Layer 15

Disable TCP timestamps persistently:

```bash
sudo tee /etc/sysctl.d/90-defensive-lab.conf >/dev/null <<'EOF'
net.ipv4.tcp_timestamps = 0
EOF
sudo sysctl --system
```

> TTL is set to 128 on outgoing IPv4 packets on the lab interface. This alone does not guarantee Nmap identifies the host as Windows — it considers multiple TCP/IP characteristics.

---

## Verification

### Inspect rules

```bash
sudo iptables -S
sudo iptables -t nat -S
sudo iptables -t mangle -S
sudo ip6tables -S
sudo ss -lntup
```

### Watch logs in real time

```bash
sudo tail -f /var/log/honeypot.log
sudo journalctl -k -f | grep 'DEF-'
sudo journalctl -u defensive-honeypot.service -f
```

### Test Modbus port

```bash
# Normal — blocked
nmap -sT -p 9000 TARGET_IP

# With source port 53 — allowed
nmap -sT -g 53 -p 9000 TARGET_IP
```

### Test Redis port knocking

```bash
# Before knocking — filtered
nmap -sT -p 9002 TARGET_IP

# Send the knock sequence
for port in 7331 4821 6150; do nc -z -w 1 TARGET_IP "$port"; done

# After knocking — open
nmap -sT -p 9002 TARGET_IP
```

### Decoy port behavior

```bash
nmap -sT -p 9101-9104,9201-9204,9301-9304 TARGET_IP
```

| Port Range | Expected Result | Reason |
|---|---|---|
| 9101–9104 | **Open** | Honeypot process listening |
| 9201–9204 | **Closed** | TCP RST returned |
| 9301–9304 | **Filtered** | Packets silently dropped |

---

## Port Knocking Flow

```
Client                                    Firewall VM
  │                                           │
  │── TCP SYN → :7331 ─────────────────────► │  DEF_KNOCK1 set, logged, dropped
  │                                           │
  │── TCP SYN → :4821 ─────────────────────► │  DEF_KNOCK1 ✓ → DEF_KNOCK2 set, dropped
  │                                           │
  │── TCP SYN → :6150 ─────────────────────► │  DEF_KNOCK2 ✓ → DEF_REDIS_AUTH set (300 s)
  │                                           │
  │── TCP SYN → :9002 ─────────────────────► │  NAT: :9002 → :6379
  │                       ◄── SYN+ACK ─────── │  DEF_REDIS_AUTH ✓ → ACCEPT
```

---

## Limitations

| Layer | Limitation |
|---|---|
| `01` | String match only catches a literal `nmap` payload — most Nmap probes carry no such string |
| `03` | Does not block every scan type; broad `! --syn` omitted to avoid breaking legitimate TCP |
| `04` | Dropping all ICMP can break Path MTU Discovery — essential types are permitted |
| `05` | Trap-port ban also blocks a legitimate admin if they touch a trap port |
| `07` | Simulators **must** listen locally on `:502` and `:6379` |
| `08` | Source port 53 is not authentication — any client can deliberately select it |
| `09` | Port knocking can be observed or replayed — not production-grade access control |
| `11` | Open decoy ports require a listening process — firewall rules alone are not enough |
| `12` | SSH/HTTP/FTP banners require manual application configuration |
| `15` | TTL 128 influences fingerprinting but does not guarantee Nmap reports a Windows host |

---

## File Structure

```
defensive-firewall-lab/
├── defensive-firewall.sh          # Main iptables rule script
├── honeypot.py                    # Python async honeypot listener
├── defensive-honeypot.service     # systemd unit for the honeypot
├── defensive-firewall.service     # systemd unit for rule persistence
├── 90-defensive-lab.conf          # sysctl — disable TCP timestamps
└── README.md
```

---

## License

For educational use in authorized lab environments only. Not intended for production deployment.
