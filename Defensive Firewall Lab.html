<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Defensive Firewall Lab</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg:        #0D1B2A;
    --panel:     #132236;
    --border:    #1E3A5F;
    --accent:    #00C8FF;
    --accent-dim:#005f80;
    --text:      #D6E8F5;
    --muted:     #6A8FAD;
    --warn:      #FFB347;
    --warn-bg:   #1f1500;
    --good:      #3DCC8E;
    --code-bg:   #0a1420;
    --radius:    4px;
    color-scheme: dark;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Inter', system-ui, sans-serif;
    font-size: 15px;
    line-height: 1.7;
    display: flex;
    min-height: 100vh;
  }

  /* ── Sidebar ── */
  nav {
    width: 240px;
    min-width: 240px;
    background: var(--panel);
    border-right: 1px solid var(--border);
    padding: 2rem 0;
    position: sticky;
    top: 0;
    height: 100vh;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
  }

  .nav-logo {
    padding: 0 1.5rem 1.5rem;
    border-bottom: 1px solid var(--border);
    margin-bottom: 1rem;
  }

  .nav-logo .shield {
    font-size: 2rem;
    line-height: 1;
    display: block;
    margin-bottom: 0.4rem;
  }

  .nav-logo span {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.72rem;
    color: var(--accent);
    letter-spacing: 0.04em;
  }

  nav a {
    display: block;
    padding: 0.35rem 1.5rem;
    color: var(--muted);
    text-decoration: none;
    font-size: 0.82rem;
    transition: color 0.15s, border-color 0.15s;
    border-left: 2px solid transparent;
  }

  nav a:hover {
    color: var(--text);
    border-left-color: var(--accent);
  }

  nav .nav-section {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.65rem;
    color: var(--accent-dim);
    padding: 1rem 1.5rem 0.25rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }

  /* ── Main content ── */
  main {
    flex: 1;
    max-width: 860px;
    padding: 3rem 3.5rem 5rem;
    min-width: 0;
  }

  /* ── Warning banner ── */
  .warn-banner {
    background: var(--warn-bg);
    border: 1px solid var(--warn);
    border-left: 4px solid var(--warn);
    border-radius: var(--radius);
    padding: 1rem 1.25rem;
    margin-bottom: 2.5rem;
    font-size: 0.88rem;
    color: var(--warn);
    line-height: 1.6;
  }

  .warn-banner strong { color: var(--warn); }

  /* ── Hero ── */
  .hero {
    padding-bottom: 2.5rem;
    border-bottom: 1px solid var(--border);
    margin-bottom: 3rem;
  }

  .hero h1 {
    font-family: 'JetBrains Mono', monospace;
    font-size: 1.9rem;
    font-weight: 500;
    color: var(--text);
    line-height: 1.25;
    margin-bottom: 0.75rem;
  }

  .hero h1 .accent { color: var(--accent); }

  .hero p {
    color: var(--muted);
    font-size: 0.95rem;
    max-width: 58ch;
  }

  .badges {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    margin-top: 1.25rem;
  }

  .badge {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.7rem;
    padding: 0.2rem 0.6rem;
    border-radius: 2px;
    background: var(--panel);
    border: 1px solid var(--border);
    color: var(--muted);
  }

  .badge.accent { border-color: var(--accent-dim); color: var(--accent); }

  /* ── Sections ── */
  section {
    margin-bottom: 3.5rem;
  }

  section h2 {
    font-family: 'JetBrains Mono', monospace;
    font-size: 1rem;
    font-weight: 500;
    color: var(--accent);
    margin-bottom: 1.25rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid var(--border);
  }

  section h3 {
    font-size: 0.9rem;
    font-weight: 600;
    color: var(--text);
    margin: 1.75rem 0 0.75rem;
  }

  section p {
    color: var(--muted);
    margin-bottom: 1rem;
    max-width: 70ch;
  }

  section p strong { color: var(--text); font-weight: 600; }

  /* ── Stack list ── */
  .stack-list {
    list-style: none;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 0.75rem;
    margin-bottom: 1rem;
  }

  .stack-list li {
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 0.65rem 0.9rem;
    font-size: 0.83rem;
    color: var(--muted);
  }

  .stack-list li code { color: var(--accent); font-family: 'JetBrains Mono', monospace; font-size: 0.8rem; }

  /* ── Code blocks ── */
  pre {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.1rem 1.25rem;
    overflow-x: auto;
    margin: 0.75rem 0 1.25rem;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.8rem;
    line-height: 1.6;
    color: #9FC5E0;
  }

  pre .cmd { color: var(--accent); }
  pre .comment { color: #3D6080; }
  pre .kw { color: var(--warn); }
  pre .val { color: var(--good); }

  code {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.82em;
    background: var(--code-bg);
    color: var(--accent);
    padding: 0.1em 0.35em;
    border-radius: 2px;
    border: 1px solid var(--border);
  }

  pre code {
    background: none;
    border: none;
    padding: 0;
    font-size: inherit;
    color: inherit;
  }

  /* ── Architecture diagram ── */
  .arch-diagram {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.5rem;
    overflow-x: auto;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.75rem;
    line-height: 1.65;
    color: #6A8FAD;
  }

  .arch-diagram .hl { color: var(--accent); }
  .arch-diagram .hl2 { color: var(--good); }
  .arch-diagram .dim { color: #2E4F6A; }

  /* ── Layers table ── */
  .layers-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.83rem;
    margin-bottom: 1rem;
  }

  .layers-table th {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.7rem;
    color: var(--muted);
    text-align: left;
    padding: 0.5rem 0.75rem;
    border-bottom: 1px solid var(--border);
    font-weight: 400;
  }

  .layers-table td {
    padding: 0.55rem 0.75rem;
    border-bottom: 1px solid #0f1e2e;
    vertical-align: top;
    color: var(--muted);
    line-height: 1.5;
  }

  .layers-table tr:hover td { background: var(--panel); }

  .layers-table .layer-num {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.72rem;
    color: var(--accent-dim);
    text-align: right;
    white-space: nowrap;
  }

  .layers-table .layer-name { color: var(--text); font-weight: 500; white-space: nowrap; }

  /* ── Port table ── */
  .data-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.83rem;
    margin: 0.75rem 0 1.5rem;
  }

  .data-table th {
    background: var(--panel);
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.7rem;
    color: var(--accent);
    padding: 0.6rem 0.9rem;
    border: 1px solid var(--border);
    font-weight: 400;
  }

  .data-table td {
    padding: 0.55rem 0.9rem;
    border: 1px solid var(--border);
    color: var(--muted);
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.8rem;
  }

  .data-table td.label { color: var(--text); font-family: 'Inter', sans-serif; font-size: 0.83rem; }

  /* ── Port knocking flow ── */
  .knock-flow {
    background: var(--code-bg);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 1.5rem;
    overflow-x: auto;
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.77rem;
    line-height: 1.9;
    color: #4A7090;
  }

  .knock-flow .client { color: var(--accent); }
  .knock-flow .server { color: var(--good); }
  .knock-flow .note   { color: var(--muted); }

  /* ── Limitations ── */
  .limits-list {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .limits-list li {
    display: grid;
    grid-template-columns: 3rem 1fr;
    gap: 0.75rem;
    align-items: start;
    font-size: 0.84rem;
    padding: 0.6rem 0;
    border-bottom: 1px solid #0f1e2e;
  }

  .limits-list .lnum {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.72rem;
    color: var(--accent-dim);
    padding-top: 0.1rem;
    text-align: right;
  }

  .limits-list .ltext { color: var(--muted); line-height: 1.55; }

  /* ── Callout ── */
  .callout {
    background: var(--panel);
    border-left: 3px solid var(--accent);
    padding: 0.85rem 1.1rem;
    border-radius: 0 var(--radius) var(--radius) 0;
    font-size: 0.84rem;
    color: var(--muted);
    margin: 1rem 0 1.5rem;
  }

  .callout strong { color: var(--text); }

  /* ── Verification results ── */
  .port-results {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 0.75rem;
    margin: 1rem 0 1.5rem;
  }

  .port-card {
    background: var(--panel);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 0.9rem 1rem;
  }

  .port-card .ports {
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.75rem;
    color: var(--accent);
    margin-bottom: 0.4rem;
  }

  .port-card .state {
    font-size: 0.8rem;
    font-weight: 600;
  }

  .port-card .state.open  { color: var(--good); }
  .port-card .state.closed { color: var(--warn); }
  .port-card .state.filtered { color: var(--muted); }

  .port-card .desc {
    font-size: 0.75rem;
    color: var(--muted);
    margin-top: 0.25rem;
  }

  /* ── Responsive ── */
  @media (max-width: 800px) {
    nav { display: none; }
    main { padding: 2rem 1.25rem 4rem; }
    .port-results { grid-template-columns: 1fr; }
    .hero h1 { font-size: 1.4rem; }
  }

  @media (prefers-reduced-motion: reduce) {
    * { transition: none !important; animation: none !important; }
  }
</style>
</head>
<body>

<!-- ── Sidebar ── -->
<nav>
  <div class="nav-logo">
    <span class="shield">🛡️</span>
    <span>defensive-firewall-lab</span>
  </div>
  <div class="nav-section">contents</div>
  <a href="#overview">Overview</a>
  <a href="#architecture">Architecture</a>
  <a href="#layers">Defense Layers</a>
  <div class="nav-section">setup</div>
  <a href="#installation">Installation</a>
  <a href="#configuration">Configuration</a>
  <a href="#honeypot">Honeypot</a>
  <a href="#persistence">Persistence</a>
  <div class="nav-section">testing</div>
  <a href="#verification">Verification</a>
  <a href="#knocking">Port Knocking Flow</a>
  <a href="#limitations">Limitations</a>
</nav>

<!-- ── Main ── -->
<main>

  <!-- Warning -->
  <div class="warn-banner">
    <strong>⚠ Authorized use only.</strong> Run this only on a machine and network you own or are authorized to administer.
    The default <code>INPUT</code> policy is <code>DROP</code> — a misconfigured <code>ADMIN_CIDR</code> will lock you out of SSH.
    Always test on a disposable VM snapshot first. Do not run from an SSH session you cannot recover.
  </div>

  <!-- Hero -->
  <div class="hero" id="overview">
    <h1><span class="accent">Defensive</span> Firewall Lab</h1>
    <p>A layered <code>iptables</code> firewall for a Linux VM hosting simulated Modbus and Redis industrial services. Demonstrates defense-in-depth — from default deny through honeypot deception and OS fingerprint confusion.</p>
    <div class="badges">
      <span class="badge accent">Lubuntu</span>
      <span class="badge">iptables</span>
      <span class="badge">conntrack</span>
      <span class="badge">Python 3</span>
      <span class="badge">systemd</span>
      <span class="badge">ICS/SCADA simulation</span>
    </div>
  </div>

  <!-- Overview -->
  <section>
    <p>This lab deploys a multi-layer defensive firewall on a Lubuntu VM that exposes simulated <strong>Modbus (TCP 502)</strong> and <strong>Redis (TCP 6379)</strong> services — both hidden behind NAT redirection, port knocking, and source-port filtering.</p>

    <h3>Stack</h3>
    <ul class="stack-list">
      <li><code>iptables</code> / <code>ip6tables</code> — packet filtering and NAT</li>
      <li><code>conntrack</code> — stateful connection tracking</li>
      <li><code>hashlimit</code> / <code>recent</code> — rate limiting and port knocking</li>
      <li><code>Python 3 asyncio</code> — honeypot listener</li>
      <li><code>systemd</code> — service hardening and persistence</li>
    </ul>
  </section>

  <!-- Architecture -->
  <section id="architecture">
    <h2># architecture</h2>
    <div class="arch-diagram"><span class="hl">[External Scanner / Attacker VM]</span>
           │
           ▼
   ┌───────────────────────┐
   │  <span class="hl">LAB_IF</span>  (iptables)  │  <span class="dim">◄── e.g. enp0s8</span>
   └──────────┬────────────┘
              │  <span class="hl2">PREROUTING (NAT)</span>
              │  :9000 → :502
              │  :9002 → :6379
              ▼
   ┌───────────────────────────────────────────┐
   │              <span class="hl">INPUT CHAIN</span>                 │
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
        <span class="hl2">[Modbus :502]</span>       <span class="hl2">[Redis :6379]</span>
        <span class="dim">[Honeypot :9101–9104]</span></div>
  </section>

  <!-- Defense layers -->
  <section id="layers">
    <h2># defense layers</h2>
    <table class="layers-table">
      <thead>
        <tr>
          <th style="width:3rem; text-align:right">layer</th>
          <th style="width:14rem">name</th>
          <th>description</th>
        </tr>
      </thead>
      <tbody>
        <tr><td class="layer-num">00</td><td class="layer-name">Default Deny</td><td><code>DROP</code> all INPUT/FORWARD; loopback and established traffic allowed</td></tr>
        <tr><td class="layer-num">01</td><td class="layer-name">Deep Packet Match</td><td>Optional literal <code>nmap</code> string match — disabled by default</td></tr>
        <tr><td class="layer-num">02</td><td class="layer-name">Stateful Filtering</td><td>Accept <code>ESTABLISHED,RELATED</code>; drop <code>INVALID</code></td></tr>
        <tr><td class="layer-num">03</td><td class="layer-name">Bad TCP Flags</td><td>Drop NULL, Xmas, FIN-only, SYN+FIN, SYN+RST probes</td></tr>
        <tr><td class="layer-num">04</td><td class="layer-name">ICMP Control</td><td>Rate-limit echo requests; allow essential ICMP error types</td></tr>
        <tr><td class="layer-num">05</td><td class="layer-name">Trap Ports</td><td>Block source IPs touching ports 21, 23, 445, 3389 for 60 s</td></tr>
        <tr><td class="layer-num">06</td><td class="layer-name">Rate Limiting</td><td>Limit new SYNs and new Modbus connections per source IP</td></tr>
        <tr><td class="layer-num">07</td><td class="layer-name">NAT Redirection</td><td>Redirect :9000 → :502 and :9002 → :6379 in PREROUTING</td></tr>
        <tr><td class="layer-num">08</td><td class="layer-name">Source-Port Filter</td><td>Modbus only allowed when client TCP source port is 53</td></tr>
        <tr><td class="layer-num">09</td><td class="layer-name">Port Knocking</td><td>Sequence 7331 → 4821 → 6150 authorizes Redis for 300 s</td></tr>
        <tr><td class="layer-num">10</td><td class="layer-name">Direct-Port Hiding</td><td>Reject :502 directly; silently drop :6379 directly</td></tr>
        <tr><td class="layer-num">11</td><td class="layer-name">Deception / Noise</td><td>4 open decoys, 4 RST ports, 4 silently filtered ports</td></tr>
        <tr><td class="layer-num">12</td><td class="layer-name">Hint Puzzle</td><td>Base64 knock-port fragments hidden in SSH/HTTP/FTP banners</td></tr>
        <tr><td class="layer-num">13</td><td class="layer-name">Honeypot Logging</td><td>Python listener logs source address and received payload</td></tr>
        <tr><td class="layer-num">14</td><td class="layer-name">Firewall Logging</td><td>Rate-limited <code>DEF-*</code> log prefixes via kernel</td></tr>
        <tr><td class="layer-num">15</td><td class="layer-name">OS Fingerprint</td><td>Outgoing IPv4 TTL set to 128; TCP timestamps disabled</td></tr>
        <tr><td class="layer-num">16</td><td class="layer-name">Service Hardening</td><td><code>systemd</code> sandbox for honeypot — no root, strict filesystem</td></tr>
        <tr><td class="layer-num">17</td><td class="layer-name">Persistence</td><td>Rules saved to <code>/etc/iptables/</code> and restored at boot</td></tr>
      </tbody>
    </table>
  </section>

  <!-- Installation -->
  <section id="installation">
    <h2># installation</h2>

    <h3>1. Prerequisites (Lubuntu / Ubuntu)</h3>
    <pre><span class="cmd">sudo apt update</span>
<span class="cmd">sudo apt install -y iptables iptables-persistent python3</span></pre>

    <h3>2. Identify your interface</h3>
    <pre><span class="cmd">ip -br address</span>
<span class="cmd">ip route</span></pre>

    <h3>3. Create and configure the firewall script</h3>
    <pre><span class="cmd">sudo install -d -m 0755 /usr/local/sbin</span>
<span class="cmd">sudo nano /usr/local/sbin/defensive-firewall.sh</span></pre>

    <p>Paste the script content, then <strong>edit the two variables at the top</strong> before running:</p>
    <pre><span class="kw">LAB_IF</span>=<span class="val">"eth1"</span>              <span class="comment"># your actual lab interface — NOT the example value</span>
<span class="kw">ADMIN_CIDR</span>=<span class="val">"192.168.56.5/32"</span> <span class="comment"># your admin machine IP</span></pre>

    <pre><span class="cmd">sudo chmod 0755 /usr/local/sbin/defensive-firewall.sh</span>
<span class="cmd">sudo /usr/local/sbin/defensive-firewall.sh</span></pre>

    <div class="callout">
      The script <strong>flushes all existing iptables rules</strong>. Run it only on a disposable lab VM.
      The safety check at the top will refuse to run if <code>LAB_IF</code> is still set to the example value <code>enp0s8</code>.
    </div>
  </section>

  <!-- Configuration -->
  <section id="configuration">
    <h2># configuration</h2>

    <h3>NAT redirection — Layer 07</h3>
    <table class="data-table">
      <thead><tr><th>External port</th><th>Redirected to</th><th>Service</th></tr></thead>
      <tbody>
        <tr><td>TCP 9000</td><td>TCP 502</td><td class="label">Modbus simulator</td></tr>
        <tr><td>TCP 9002</td><td>TCP 6379</td><td class="label">Redis simulator</td></tr>
      </tbody>
    </table>
    <p>The simulators must listen locally on <code>:502</code> and <code>:6379</code>. Confirm with:</p>
    <pre><span class="cmd">sudo ss -lntup</span></pre>

    <h3>Source-port condition — Layer 08</h3>
    <p>Modbus (<code>:9000</code>) only accepts connections where the client's <strong>TCP source port is 53</strong>.</p>
    <pre><span class="comment"># Blocked — normal ephemeral source port</span>
<span class="cmd">nmap -sT -p 9000 TARGET_IP</span>

<span class="comment"># Allowed — source port 53</span>
<span class="cmd">nmap -sT -g 53 -p 9000 TARGET_IP</span></pre>

    <h3>Port knocking — Layer 09</h3>
    <p>Send all three knocks from the <strong>same source IP</strong>, in order, within 10-second windows:</p>
    <pre><span class="kw">for</span> port <span class="kw">in</span> <span class="val">7331 4821 6150</span>; <span class="kw">do</span>
  <span class="cmd">nc -z -w 1 TARGET_IP "$port"</span>
<span class="kw">done</span></pre>
    <p>This grants access to Redis (<code>:9002</code>) for <strong>300 seconds</strong>.</p>

    <h3>Hint puzzle — Layer 12</h3>
    <p>Generate Base64 fragments for embedding in service banners:</p>
    <pre><span class="cmd">printf '%s' '7331' | base64</span>  <span class="comment"># → NzMzMQ==</span>
<span class="cmd">printf '%s' '4821' | base64</span>  <span class="comment"># → NDgyMQ==</span>
<span class="cmd">printf '%s' '6150' | base64</span>  <span class="comment"># → NjE1MA==</span></pre>
    <div class="callout">Base64 is encoding, not encryption. Anyone who obtains the fragments can decode them immediately.</div>
  </section>

  <!-- Honeypot -->
  <section id="honeypot">
    <h2># honeypot service</h2>

    <p>A process must listen for a port to appear open — firewall rules alone cannot do this. The Python honeypot listens on TCP <strong>9101–9104</strong>, logs the source address and received payload.</p>

    <h3>Create the service directory and account</h3>
    <pre><span class="cmd">sudo install -d -o root -g root -m 0755 /opt/defensive-lab</span>
<span class="cmd">sudo useradd --system --no-create-home --shell /usr/bin/nologin svchoney</span>
<span class="cmd">sudo install -o svchoney -g svchoney -m 0640 /dev/null /var/log/honeypot.log</span></pre>

    <h3>systemd unit hardening — Layer 16</h3>
    <p>Create <code>/etc/systemd/system/defensive-honeypot.service</code>:</p>
    <pre><span class="comment">[Service]</span>
<span class="kw">User</span>=<span class="val">svchoney</span>
<span class="kw">NoNewPrivileges</span>=<span class="val">true</span>
<span class="kw">ProtectSystem</span>=<span class="val">strict</span>
<span class="kw">ProtectHome</span>=<span class="val">true</span>
<span class="kw">PrivateTmp</span>=<span class="val">true</span>
<span class="kw">PrivateDevices</span>=<span class="val">true</span>
<span class="kw">ProtectKernelTunables</span>=<span class="val">true</span>
<span class="kw">ProtectControlGroups</span>=<span class="val">true</span>
<span class="kw">RestrictSUIDSGID</span>=<span class="val">true</span>
<span class="kw">LockPersonality</span>=<span class="val">true</span>
<span class="kw">ReadWritePaths</span>=<span class="val">/var/log/honeypot.log</span></pre>

    <pre><span class="cmd">sudo systemctl daemon-reload</span>
<span class="cmd">sudo systemctl enable --now defensive-honeypot.service</span>
<span class="cmd">sudo systemctl status defensive-honeypot.service</span></pre>
  </section>

  <!-- Persistence -->
  <section id="persistence">
    <h2># persistence</h2>

    <h3>Save rules — Layer 17</h3>
    <p><code>iptables-persistent</code> (installed above) handles save and restore automatically on Lubuntu/Ubuntu.</p>
    <pre><span class="comment"># Save current rules (run after applying the firewall script)</span>
<span class="cmd">sudo netfilter-persistent save</span></pre>

    <p>Rules are restored automatically at boot via the <code>netfilter-persistent</code> service. To reload after editing the script:</p>
    <pre><span class="cmd">sudo /usr/local/sbin/defensive-firewall.sh</span>
<span class="cmd">sudo netfilter-persistent save</span></pre>

    <p>Alternatively, create a dedicated systemd drop-in if you prefer the script to run at boot:</p>
    <pre><span class="cmd">sudo systemctl enable netfilter-persistent</span></pre>

    <h3>OS fingerprint confusion — Layer 15</h3>
    <p>Disable TCP timestamps persistently:</p>
    <pre><span class="cmd">sudo tee /etc/sysctl.d/90-defensive-lab.conf &gt;/dev/null &lt;&lt;'EOF'</span>
<span class="val">net.ipv4.tcp_timestamps = 0</span>
<span class="cmd">EOF</span>
<span class="cmd">sudo sysctl --system</span></pre>
    <div class="callout">TTL 128 is set on outgoing packets. This alone does not guarantee Nmap identifies the host as Windows — it considers multiple TCP/IP characteristics.</div>
  </section>

  <!-- Verification -->
  <section id="verification">
    <h2># verification</h2>

    <h3>Inspect rules and listening services</h3>
    <pre><span class="cmd">sudo iptables -S</span>
<span class="cmd">sudo iptables -t nat -S</span>
<span class="cmd">sudo iptables -t mangle -S</span>
<span class="cmd">sudo ip6tables -S</span>
<span class="cmd">sudo ss -lntup</span></pre>

    <h3>Watch logs in real time</h3>
    <pre><span class="cmd">sudo tail -f /var/log/honeypot.log</span>
<span class="cmd">sudo journalctl -k -f | grep 'DEF-'</span>
<span class="cmd">sudo journalctl -u defensive-honeypot.service -f</span></pre>

    <h3>Decoy port behavior</h3>
    <pre><span class="cmd">nmap -sT -p 9101-9104,9201-9204,9301-9304 TARGET_IP</span></pre>

    <div class="port-results">
      <div class="port-card">
        <div class="ports">9101 – 9104</div>
        <div class="state open">Open</div>
        <div class="desc">Honeypot process running</div>
      </div>
      <div class="port-card">
        <div class="ports">9201 – 9204</div>
        <div class="state closed">Closed</div>
        <div class="desc">TCP RST returned</div>
      </div>
      <div class="port-card">
        <div class="ports">9301 – 9304</div>
        <div class="state filtered">Filtered</div>
        <div class="desc">Packets silently dropped</div>
      </div>
    </div>
  </section>

  <!-- Port knocking flow -->
  <section id="knocking">
    <h2># port knocking flow</h2>
    <div class="knock-flow"><span class="client">Client</span>                                    <span class="server">Firewall VM</span>
  │                                              │
  │── TCP SYN → :7331 ──────────────────────► │  <span class="note">DEF_KNOCK1 set, logged, dropped</span>
  │                                              │
  │── TCP SYN → :4821 ──────────────────────► │  <span class="note">DEF_KNOCK1 ✓  →  DEF_KNOCK2 set, dropped</span>
  │                                              │
  │── TCP SYN → :6150 ──────────────────────► │  <span class="note">DEF_KNOCK2 ✓  →  DEF_REDIS_AUTH set (300 s)</span>
  │                                              │
  │── TCP SYN → :9002 ──────────────────────► │  <span class="note">NAT: :9002 → :6379</span>
  │                        ◄── SYN+ACK ──────── │  <span class="note">DEF_REDIS_AUTH ✓  →  ACCEPT</span></div>
  </section>

  <!-- Limitations -->
  <section id="limitations">
    <h2># limitations</h2>
    <ul class="limits-list">
      <li><span class="lnum">01</span><span class="ltext">String match only catches a literal <code>nmap</code> payload — most Nmap probes carry no such string.</span></li>
      <li><span class="lnum">03</span><span class="ltext">Does not block every scan type. A broad <code>! --syn</code> rule is intentionally omitted to avoid breaking legitimate TCP.</span></li>
      <li><span class="lnum">04</span><span class="ltext">Dropping all ICMP can break Path MTU Discovery. Essential ICMP error types are permitted.</span></li>
      <li><span class="lnum">05</span><span class="ltext">Trap-port ban applies to all sources — a legitimate administrator touching a trap port gets blocked too.</span></li>
      <li><span class="lnum">07</span><span class="ltext">Simulators must listen locally on <code>:502</code> and <code>:6379</code>. Confirm with <code>ss -lntup</code>.</span></li>
      <li><span class="lnum">08</span><span class="ltext">Source port 53 is not authentication. Any client can deliberately select it.</span></li>
      <li><span class="lnum">09</span><span class="ltext">Port knocking can be observed or replayed. Use it as a lab mechanism, not as sole production access control.</span></li>
      <li><span class="lnum">11</span><span class="ltext">Open decoy ports require a listening process. Firewall rules alone cannot make a port appear open.</span></li>
      <li><span class="lnum">12</span><span class="ltext">SSH/HTTP/FTP banners require manual application configuration — the firewall cannot create them.</span></li>
      <li><span class="lnum">15</span><span class="ltext">TTL 128 influences fingerprinting but does not guarantee Nmap reports a Windows host.</span></li>
    </ul>
  </section>

  <!-- Footer -->
  <div style="margin-top:3rem; padding-top:1.5rem; border-top:1px solid var(--border); font-size:0.78rem; color:var(--muted); font-family:'JetBrains Mono',monospace;">
    For educational use in authorized lab environments only. Not intended for production deployment.
  </div>

</main>
</body>
</html>
