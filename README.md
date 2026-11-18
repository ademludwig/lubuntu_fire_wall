🛡️ defensive-firewall-lab
contents
Overview Architecture Defense Layers
setup
Installation Configuration Honeypot Persistence
testing
Verification Port Knocking Flow Limitations
⚠ Authorized use only. Run this only on a machine and network you own or are authorized to administer. The default `INPUT` policy is `DROP` — a misconfigured `ADMIN_CIDR` will lock you out of SSH. Always test on a disposable VM snapshot first. Do not run from an SSH session you cannot recover.
Defensive Firewall Lab
A layered `iptables` firewall for a Linux VM hosting simulated Modbus and Redis industrial services. Demonstrates defense-in-depth — from default deny through honeypot deception and OS fingerprint confusion.
Arch Linux iptables conntrack Python 3 systemd ICS/SCADA simulation
This lab deploys a multi-layer defensive firewall on an Arch Linux VM that exposes simulated Modbus (TCP 502) and Redis (TCP 6379) services — both hidden behind NAT redirection, port knocking, and source-port filtering.
Stack
`iptables` / `ip6tables` — packet filtering and NAT
`conntrack` — stateful connection tracking
`hashlimit` / `recent` — rate limiting and port knocking
`Python 3 asyncio` — honeypot listener
`systemd` — service hardening and persistence
# architecture
[External Scanner / Attacker VM] │ ▼ ┌───────────────────────┐ │ LAB_IF (iptables) │ ◄── e.g. enp0s8 └──────────┬────────────┘ │ PREROUTING (NAT) │ :9000 → :502 │ :9002 → :6379 ▼ ┌───────────────────────────────────────────┐ │ INPUT CHAIN │ │ 00. Default DROP │ │ 02. conntrack (ESTABLISHED / INVALID) │ │ 03. Bad TCP flags → DROP │ │ 04. ICMP rate limit │ │ 05. Trap ports 21, 23, 445, 3389 │ │ 06. SYN + Modbus rate limits │ │ 08. Source-port check (sport 53) │ │ 09. Port knocking → Redis access │ │ 10. Direct port blocking │ │ 11. Decoy ports (open / RST / filtered) │ │ 14. Catch-all logging │ └────────────┬────────────────┬─────────────┘ ▼ ▼ [Modbus :502] [Redis :6379] [Honeypot :9101–9104]
# defense layers
layer	name	description
00	Default Deny	`DROP` all INPUT/FORWARD; loopback and established traffic allowed
01	Deep Packet Match	Optional literal `nmap` string match — disabled by default
02	Stateful Filtering	Accept `ESTABLISHED,RELATED`; drop `INVALID`
03	Bad TCP Flags	Drop NULL, Xmas, FIN-only, SYN+FIN, SYN+RST probes
04	ICMP Control	Rate-limit echo requests; allow essential ICMP error types
05	Trap Ports	Block source IPs touching ports 21, 23, 445, 3389 for 60 s
06	Rate Limiting	Limit new SYNs and new Modbus connections per source IP
07	NAT Redirection	Redirect :9000 → :502 and :9002 → :6379 in PREROUTING
08	Source-Port Filter	Modbus only allowed when client TCP source port is 53
09	Port Knocking	Sequence 7331 → 4821 → 6150 authorizes Redis for 300 s
10	Direct-Port Hiding	Reject :502 directly; silently drop :6379 directly
11	Deception / Noise	4 open decoys, 4 RST ports, 4 silently filtered ports
12	Hint Puzzle	Base64 knock-port fragments hidden in SSH/HTTP/FTP banners
13	Honeypot Logging	Python listener logs source address and received payload
14	Firewall Logging	Rate-limited `DEF-*` log prefixes via kernel
15	OS Fingerprint	Outgoing IPv4 TTL set to 128; TCP timestamps disabled
16	Service Hardening	`systemd` sandbox for honeypot — no root, strict filesystem
17	Persistence	Rules saved to `/etc/iptables/` and restored at boot
# installation
1. Prerequisites (Arch Linux)
```
sudo pacman -S --needed iptables python
```
2. Identify your interface
```
ip -br address
ip route
```
3. Create and configure the firewall script
```
sudo install -d -m 0755 /usr/local/sbin
sudo nano /usr/local/sbin/defensive-firewall.sh
```
Paste the script content, then edit the two variables at the top before running:
```
LAB_IF="eth1"              # your actual lab interface — NOT the example value
ADMIN_CIDR="192.168.56.5/32" # your admin machine IP
```
```
sudo chmod 0755 /usr/local/sbin/defensive-firewall.sh
sudo /usr/local/sbin/defensive-firewall.sh
```
The script flushes all existing iptables rules. Run it only on a disposable lab VM. The safety check at the top will refuse to run if `LAB_IF` is still set to the example value `enp0s8`.
# configuration
NAT redirection — Layer 07
External port	Redirected to	Service
TCP 9000	TCP 502	Modbus simulator
TCP 9002	TCP 6379	Redis simulator
The simulators must listen locally on `:502` and `:6379`. Confirm with:
```
sudo ss -lntup
```
Source-port condition — Layer 08
Modbus (`:9000`) only accepts connections where the client's TCP source port is 53.
```
# Blocked — normal ephemeral source port
nmap -sT -p 9000 TARGET_IP

# Allowed — source port 53
nmap -sT -g 53 -p 9000 TARGET_IP
```
Port knocking — Layer 09
Send all three knocks from the same source IP, in order, within 10-second windows:
```
for port in 7331 4821 6150; do
  nc -z -w 1 TARGET_IP "$port"
done
```
This grants access to Redis (`:9002`) for 300 seconds.
Hint puzzle — Layer 12
Generate Base64 fragments for embedding in service banners:
```
printf '%s' '7331' | base64  # → NzMzMQ==
printf '%s' '4821' | base64  # → NDgyMQ==
printf '%s' '6150' | base64  # → NjE1MA==
```
Base64 is encoding, not encryption. Anyone who obtains the fragments can decode them immediately.
# honeypot service
A process must listen for a port to appear open — firewall rules alone cannot do this. The Python honeypot listens on TCP 9101–9104, logs the source address and received payload.
Create the service directory and account
```
sudo install -d -o root -g root -m 0755 /opt/defensive-lab
sudo useradd --system --no-create-home --shell /usr/bin/nologin svchoney
sudo install -o svchoney -g svchoney -m 0640 /dev/null /var/log/honeypot.log
```
systemd unit hardening — Layer 16
Create `/etc/systemd/system/defensive-honeypot.service`:
```
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
```
sudo systemctl daemon-reload
sudo systemctl enable --now defensive-honeypot.service
sudo systemctl status defensive-honeypot.service
```
# persistence
Save rules — Layer 17
```
sudo install -d -m 0755 /etc/iptables
sudo iptables-save  | sudo tee /etc/iptables/iptables.rules  >/dev/null
sudo ip6tables-save | sudo tee /etc/iptables/ip6tables.rules >/dev/null
```
Create `/etc/systemd/system/defensive-firewall.service` and enable it:
```
sudo systemctl daemon-reload
sudo systemctl enable --now defensive-firewall.service
```
After editing the script, reload rules:
```
sudo systemctl restart defensive-firewall.service
```
OS fingerprint confusion — Layer 15
Disable TCP timestamps persistently:
```
sudo tee /etc/sysctl.d/90-defensive-lab.conf >/dev/null <<'EOF'
net.ipv4.tcp_timestamps = 0
EOF
sudo sysctl --system
```
TTL 128 is set on outgoing packets. This alone does not guarantee Nmap identifies the host as Windows — it considers multiple TCP/IP characteristics.
# verification
Inspect rules and listening services
```
sudo iptables -S
sudo iptables -t nat -S
sudo iptables -t mangle -S
sudo ip6tables -S
sudo ss -lntup
```
Watch logs in real time
```
sudo tail -f /var/log/honeypot.log
sudo journalctl -k -f | grep 'DEF-'
sudo journalctl -u defensive-honeypot.service -f
```
Decoy port behavior
```
nmap -sT -p 9101-9104,9201-9204,9301-9304 TARGET_IP
```
9101 – 9104
Open
Honeypot process running
9201 – 9204
Closed
TCP RST returned
9301 – 9304
Filtered
Packets silently dropped
# port knocking flow
Client Firewall VM │ │ │── TCP SYN → :7331 ──────────────────────► │ DEF_KNOCK1 set, logged, dropped │ │ │── TCP SYN → :4821 ──────────────────────► │ DEF_KNOCK1 ✓ → DEF_KNOCK2 set, dropped │ │ │── TCP SYN → :6150 ──────────────────────► │ DEF_KNOCK2 ✓ → DEF_REDIS_AUTH set (300 s) │ │ │── TCP SYN → :9002 ──────────────────────► │ NAT: :9002 → :6379 │ ◄── SYN+ACK ──────── │ DEF_REDIS_AUTH ✓ → ACCEPT
# limitations
01String match only catches a literal `nmap` payload — most Nmap probes carry no such string.
03Does not block every scan type. A broad `! --syn` rule is intentionally omitted to avoid breaking legitimate TCP.
04Dropping all ICMP can break Path MTU Discovery. Essential ICMP error types are permitted.
05Trap-port ban applies to all sources — a legitimate administrator touching a trap port gets blocked too.
07Simulators must listen locally on `:502` and `:6379`. Confirm with `ss -lntup`.
08Source port 53 is not authentication. Any client can deliberately select it.
09Port knocking can be observed or replayed. Use it as a lab mechanism, not as sole production access control.
11Open decoy ports require a listening process. Firewall rules alone cannot make a port appear open.
12SSH/HTTP/FTP banners require manual application configuration — the firewall cannot create them.
15TTL 128 influences fingerprinting but does not guarantee Nmap reports a Windows host.
For educational use in authorized lab environments only. Not intended for production deployment.
