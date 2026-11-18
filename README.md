# 🛡️ Defensive Firewall Lab

> A layered `iptables` firewall for a Linux VM simulating hardened Modbus and Redis industrial services.  
> Built for educational use in isolated lab environments.

---

## ⚠️ Warning

**Run this only on a machine and network you own or are authorized to administer.**  
The default `INPUT` policy is `DROP`. A misconfigured `ADMIN_CIDR` can lock you out of SSH.  
**Always test on a disposable VM snapshot first.**

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Defense Layers](#defense-layers)
- [Installation](#installation)
- [Configuration](#configuration)
- [Honeypot Service](#honeypot-service)
- [Persistence](#persistence)
- [Verification](#verification)
- [Port Knocking Flow](#port-knocking-flow)
- [Limitations](#limitations)

---

## Overview

This lab deploys a **multi-layer defensive firewall** on an Arch Linux VM that exposes simulated Modbus (TCP 502) and Redis (TCP 6379) services — both hidden behind NAT redirection, port knocking, and source-port filtering. It is designed to demonstrate real-world defense-in-depth techniques against reconnaissance and lateral movement.

**Stack:**
- `iptables` / `ip6tables` — packet filtering and NAT
- `conntrack` — stateful connection tracking
- `hashlimit` / `recent` — rate limiting and port knocking
- Python 3 `asyncio` — honeypot listener
- `systemd` — service hardening and persistence

---

## Architecture

```
[External Scanner / Attacker VM]
           │
           ▼
   ┌───────────────┐
   │  LAB_IF       │  ◄── e.g. enp0s8
   │  (iptables)   │
   └──────┬────────┘
          │  PREROUTING (NAT)
          │  :9000 → :502
          │  :9002 → :6379
          ▼
   ┌───────────────────────────────────────┐
   │           INPUT CHAIN                 │
   │  00. Default DROP                     │
   │  02. conntrack (ESTABLISHED/INVALID)  │
   │  03. Bad TCP flags                    │
   │  04. ICMP rate limit                  │
   │  05. Trap ports (21,23,445,3389)      │
   │  06. SYN + Modbus rate limits         │
   │  08. Source-port check (sport 53)     │
   │  09. Port knocking → Redis access     │
   │  10. Direct port blocking             │
   │  11. Decoy ports (open/RST/filtered)  │
   │  14. Catch-all logging                │
   └──────┬──────────────────┬────────────┘
          ▼                  ▼
   [Modbus :502]        [Redis :6379]
   [Honeypot :9101-9104]
```

---

## Defense Layers

| Layer | Name | Description |
|------:|------|-------------|
| 00 | **Default Deny** | `DROP` all INPUT/FORWARD; allow loopback and established |
| 01 | Deep Packet Matching | Optional literal `nmap` string match (disabled by default) |
| 02 | **Stateful Filtering** | Accept `ESTABLISHED,RELATED`; drop `INVALID` |
| 03 | **Bad TCP Flags** | Drop NULL, Xmas, FIN-only, SYN+FIN, SYN+RST |
| 04 | **ICMP Control** | Rate-limit echo requests; allow essential ICMP errors |
| 05 | **Trap Ports** | Block source IPs touching ports 21, 23, 445, 3389 for 60s |
| 06 | **Rate Limiting** | Limit new SYNs and new Modbus connections per source IP |
| 07 | **NAT Redirection** | Redirect :9000 → :502 and :9002 → :6379 in PREROUTING |
| 08 | **Source-Port Filter** | Modbus only allowed when client source port is 53 |
| 09 | **Port Knocking** | Sequence 7331 → 4821 → 6150 authorizes Redis for 300s |
| 10 | **Direct-Port Hiding** | Reject :502 directly; silently drop :6379 directly |
| 11 | **Deception / Noise** | 4 open decoys, 4 RST ports, 4 silently filtered ports |
| 12 | **Hint Puzzle** | Base64 fragments hidden in SSH/HTTP/FTP banners |
| 13 | **Honeypot Logging** | Python listener logs connections and payloads |
| 14 | **Firewall Logging** | Rate-limited `DEF-*` log prefixes via kernel |
| 15 | **OS Fingerprint Confusion** | TTL set to 128; TCP timestamps disabled |
| 16 | **Service Hardening** | `systemd` sandbox for honeypot (no root, strict filesystem) |
| 17 | **Persistence** | Rules saved and restored at boot via `systemd` |

---

## Installation

### 1. Prerequisites (Arch Linux)

```bash
sudo pacman -S --needed iptables python
```

### 2. Identify your interface

```bash
ip -br address
ip route
```

### 3. Install the firewall script

```bash
sudo install -d -m 0755 /usr/local/sbin
sudo nano /usr/local/sbin/defensive-firewall.sh
```

Paste the script content, **edit the two variables at the top**:

```bash
LAB_IF="enp0s8"       # ← your actual lab interface
ADMIN_CIDR="192.168.56.0/24"  # ← your admin machine IP/CIDR
```

```bash
sudo chmod 0755 /usr/local/sbin/defensive-firewall.sh
sudo /usr/local/sbin/defensive-firewall.sh
```

> The script **flushes all existing iptables rules**. Only run it on a disposable lab VM.

---

## Configuration

### NAT Redirection (Layer 07)

| External Port | Redirected To | Service |
|:---:|:---:|---|
| TCP 9000 | TCP 502 | Modbus simulator |
| TCP 9002 | TCP 6379 | Redis simulator |

The simulators must listen locally on `:502` and `:6379`. Verify with:

```bash
sudo ss -lntup
```

### Source-Port Condition (Layer 08)

Modbus (`:9000`) only accepts connections where the **client's source port is 53**.

```bash
# Normal connection — blocked
nmap -sT -p 9000 TARGET_IP

# With source port 53 — allowed
nmap -sT -g 53 -p 9000 TARGET_IP
```

### Port Knocking — Redis Access (Layer 09)

Send all three knocks from the **same source IP**, in order, within 10-second windows:

```bash
for port in 7331 4821 6150; do
  nc -z -w 1 TARGET_IP "$port"
done
```

This grants access to Redis (`:9002`) for **300 seconds**.

### Hint Puzzle — Banner Fragments (Layer 12)

Generate the Base64-encoded knock ports for embedding in service banners:

```bash
printf '%s' '7331' | base64   # → NzMzMQ==
printf '%s' '4821' | base64   # → NDgyMQ==
printf '%s' '6150' | base64   # → NjE1MA==
```

Place one fragment in each of the SSH / HTTP / FTP service banners.  
*Base64 is encoding, not encryption — anyone with the fragments can decode them.*

---

## Honeypot Service

### Python Listener (Layers 11 & 13)

Create the service:

```bash
sudo install -d -o root -g root -m 0755 /opt/defensive-lab
sudo nano /opt/defensive-lab/honeypot.py
```

Paste the honeypot script (listens on TCP 9101–9104, logs source + payload).

Create the log file and restricted service account:

```bash
sudo useradd --system --no-create-home --shell /usr/bin/nologin svchoney
sudo install -o svchoney -g svchoney -m 0640 /dev/null /var/log/honeypot.log
sudo chown -R root:root /opt/defensive-lab
sudo chmod 0755 /opt/defensive-lab
sudo chmod 0644 /opt/defensive-lab/honeypot.py
```

### systemd Unit (Layer 16)

Create `/etc/systemd/system/defensive-honeypot.service` with the following hardening:

```ini
[Service]
User=svchoney
NoNewPrivileges=true
ProtectSystem=strict
ProtectHome=true
PrivateTmp=true
PrivateDevices=true
ProtectKernelTunables=true
ProtectControlGroups=true
RestrictSUIDSGID=true
LockPersonality=true
ReadWritePaths=/var/log/honeypot.log
```

Enable it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now defensive-honeypot.service
sudo systemctl status defensive-honeypot.service
```

---

## Persistence

### Save and Restore Rules (Layer 17)

```bash
sudo install -d -m 0755 /etc/iptables
sudo iptables-save  | sudo tee /etc/iptables/iptables.rules  >/dev/null
sudo ip6tables-save | sudo tee /etc/iptables/ip6tables.rules >/dev/null
```

Create `/etc/systemd/system/defensive-firewall.service` and enable it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now defensive-firewall.service
```

After editing the script, reload rules:

```bash
sudo systemctl restart defensive-firewall.service
```

### OS Fingerprint Confusion (Layer 15)

Disable TCP timestamps persistently:

```bash
sudo tee /etc/sysctl.d/90-defensive-lab.conf >/dev/null <<'EOF'
net.ipv4.tcp_timestamps = 0
EOF
sudo sysctl --system
```

> TTL is set to 128 on outgoing packets. This alone does not guarantee a Windows fingerprint.

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

### Test decoy port behavior

```bash
nmap -sT -p 9101-9104,9201-9204,9301-9304 TARGET_IP
```

| Port Range | Expected Result |
|---|---|
| 9101–9104 | **Open** (honeypot running) |
| 9201–9204 | **Closed** — TCP RST |
| 9301–9304 | **Filtered** — silent drop |

---

## Port Knocking Flow

```
Client                              Firewall VM
  │                                     │
  │── TCP SYN → :7331 ────────────────► │  DEF_KNOCK1 set, logged, dropped
  │                                     │
  │── TCP SYN → :4821 ────────────────► │  DEF_KNOCK1 ✓ → DEF_KNOCK2 set, dropped
  │                                     │
  │── TCP SYN → :6150 ────────────────► │  DEF_KNOCK2 ✓ → DEF_REDIS_AUTH set (300s)
  │                                     │
  │── TCP SYN → :9002 ────────────────► │  NAT: :9002 → :6379
  │                    ◄─── SYN+ACK ─── │  DEF_REDIS_AUTH ✓ → ACCEPT
```

---

## Limitations

| Layer | Limitation |
|---|---|
| 01 | String match only catches literal `nmap` payloads — not a real scanner detector |
| 03 | Does not block every scan type (broad `! --syn` omitted to avoid breaking TCP) |
| 04 | Dropping all ICMP can break Path MTU Discovery — essential types are permitted |
| 05 | Trap-port ban also blocks a legitimate admin if they touch a trap port |
| 07 | Simulators **must** listen locally on `:502` and `:6379` |
| 08 | Source port 53 is not authentication — it can be spoofed |
| 09 | Port knocking can be observed or replayed — not production-grade access control |
| 11 | Open decoy ports require a listening process — firewall rules alone are not enough |
| 12 | SSH/HTTP/FTP banners require manual application configuration |
| 15 | TTL 128 influences fingerprinting but does not guarantee Nmap sees Windows |

---

## License

For educational use in authorized lab environments only. Not intended for production deployment.
