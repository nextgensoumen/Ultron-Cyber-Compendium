# Nmap

## Overview

**Tool Name**: Nmap (Network Mapper)
**Tool Type/Category**: Network Scanner / Port Scanner / Enumeration Tool
**Developer/Organization**: Gordon Lyon (Fyodor) — maintained by the Nmap Project
**Operating Systems**: Linux, Windows, macOS, BSD, Solaris
**Industry Usage Level**: Beginner → Professional (used at all levels)
**Workflow Phase**: Reconnaissance → Scanning → Enumeration

Nmap is one of the most iconic and universally used tools in cybersecurity. It has been featured in major films, referenced in security certifications like CEH, OSCP, CompTIA Security+, and is the de facto standard for network discovery and security auditing across the entire industry.

## Purpose

**Why it exists:**
Before any security assessment can begin, a professional must answer a fundamental question: What is on the network? Nmap was built to answer exactly that — and far more.

**What security problem it solves:**
Networks are complex. Organizations often have hundreds or thousands of devices, open ports, running services, and operating systems — many of which IT teams themselves are unaware of. Nmap solves the problem of network visibility. Without visibility, you cannot defend what you cannot see.
Specifically, Nmap solves:

- Unknown device discovery on networks
- Identification of open, closed, and filtered ports
- Detection of running services and their versions
- OS fingerprinting of remote hosts
- Detection of firewall rules and packet filtering
- Identification of potential vulnerabilities via scripting

**Why professionals use it:**

- It is free, open source, and battle-tested over decades
- It works across virtually every network environment
- It produces structured, reliable, repeatable output
- It integrates with nearly every other security tool in the industry
- It is trusted in legal penetration tests, red team engagements, blue team audits, and compliance assessments

## Installation

**Linux / Kali Linux**
Nmap is pre-installed on Kali Linux. To verify or install:

```bash
# Check if already installed
nmap --version

# Install via apt (Debian/Ubuntu/Kali)
sudo apt update && sudo apt install nmap -y

# Install via yum (CentOS/RHEL)
sudo yum install nmap -y

# Install via dnf (Fedora)
sudo dnf install nmap -y

# Install from source (latest version)
wget https://nmap.org/dist/nmap-7.95.tar.bz2
bzip2 -d nmap-7.95.tar.bz2
tar xvf nmap-7.95.tar
cd nmap-7.95
./configure
make
sudo make install
```

Verify installation:
```bash
nmap --version
# Expected output: Nmap version 7.9x ( https://nmap.org )

which nmap
# Expected output: /usr/bin/nmap
```

**Windows**
```powershell
# Method 1: Download the official installer
# Visit: https://nmap.org/download.html
# Download: nmap-7.95-setup.exe
# Run installer — includes Zenmap GUI, Ncat, and Ndiff

# Method 2: Using Chocolatey package manager
choco install nmap

# Method 3: Using Winget
winget install -e --id Insecure.Nmap

# Verify installation (PowerShell or CMD)
nmap --version
```

Note: On Windows, Nmap requires Npcap (packet capture library), which is bundled with the official installer. Without Npcap, raw packet scanning will not work.

**macOS**
```bash
# Method 1: Using Homebrew (recommended)
brew install nmap

# Method 2: Using MacPorts
sudo port install nmap

# Method 3: Official .dmg installer
# Visit: https://nmap.org/download.html
# Download and install the .dmg package

# Verify installation
nmap --version
```

## Commands

**Basic Commands**

Command 1: Simple Host Scan
```bash
nmap 192.168.1.1
```
Explanation:
- Scans the most common 1000 TCP ports on the target
- Performs default host discovery (ICMP + TCP)
- Reports port states and basic service names
- Use when: You need a quick overview of a single host

Command 2: Ping Sweep (Host Discovery)
```bash
nmap -sn 192.168.1.0/24
```
Explanation:
- `-sn` = Disable port scan (previously -sP) — only performs host discovery
- `192.168.1.0/24` = Entire /24 subnet (256 addresses)
- Reports which hosts are alive
- Use when: You want to map all live hosts before scanning ports

Command 3: Scan Specific Ports
```bash
nmap -p 22,80,443,3306 192.168.1.1
```
Explanation:
- `-p` = Specify ports to scan
- `22,80,443,3306` = SSH, HTTP, HTTPS, MySQL
- Only scans the listed ports
- Use when: You know which services you're looking for

Command 4: Scan Port Range
```bash
nmap -p 1-1000 192.168.1.1
```
Explanation:
- `-p 1-1000` = Scans all ports from 1 to 1000
- Use when: You want broader coverage than the default 1000 ports

Command 5: Scan All 65535 Ports
```bash
nmap -p- 192.168.1.1
```
Explanation:
- `-p-` = Shorthand for -p 1-65535
- Scans every single TCP port
- Takes significantly longer
- Use when: You need complete port coverage — required in thorough penetration tests


**Intermediate Commands**

Command 6: SYN Scan (Stealth Scan)
```bash
nmap -sS 192.168.1.1
```
Explanation:
- `-sS` = TCP SYN scan (half-open scan)
- Sends SYN packet, waits for SYN-ACK (open) or RST (closed)
- Never completes the TCP three-way handshake
- Faster and less likely to appear in application logs
- Requires root/administrator privileges
- Use when: You want a fast, relatively quiet scan

HOW IT WORKS:
```
Nmap ──── SYN ────► Target
Nmap ◄── SYN/ACK ── Target  (Port Open)
Nmap ──── RST ────► Target  (Never completes connection)
```

Command 7: Service Version Detection
```bash
nmap -sV 192.168.1.1
```
Explanation:
- `-sV` = Enable service/version detection
- Sends additional probes to open ports
- Identifies service name, version number, and extra info
- Result example: 80/tcp open http Apache httpd 2.4.41 ((Ubuntu))
- Use when: You need to identify specific software versions for vulnerability research

Command 8: OS Detection
```bash
nmap -O 192.168.1.1
```
Explanation:
- `-O` = Enable OS detection
- Requires root privileges
- Uses TCP/IP stack fingerprinting
- Reports OS name, version, and confidence percentage
- Result example: OS: Linux 4.15 - 5.6
- Use when: You need to understand the target environment during enumeration

Command 9: Aggressive Scan
```bash
nmap -A 192.168.1.1
```
Explanation:
- `-A` = Aggressive scan — enables multiple features simultaneously:
  - `-O` OS detection
  - `-sV` Version detection
  - `-sC` Default NSE scripts
  - `--traceroute` Network path tracing
- Very noisy — will likely trigger IDS/IPS alerts
- Use when: You need comprehensive information and stealth is not a concern

Command 10: UDP Scan
```bash
nmap -sU 192.168.1.1
```
Explanation:
- `-sU` = UDP port scan
- UDP services are commonly overlooked but critical (DNS-53, SNMP-161, TFTP-69, NTP-123)
- Much slower than TCP scans because closed UDP ports return ICMP unreachable
- Use when: You need to find UDP services that are often missed by default TCP scans

Command 11: Scan Multiple Targets with Output
```bash
nmap -sV -p 1-65535 192.168.1.0/24 -oA full_scan_results
```
Explanation:
- `-sV` = Version detection
- `-p 1-65535` = All ports
- `192.168.1.0/24` = Full subnet
- `-oA full_scan_results` = Output all formats simultaneously:
  - `full_scan_results.nmap` = Normal human-readable output
  - `full_scan_results.xml` = XML format for tool parsing
  - `full_scan_results.gnmap` = Grepable format for text processing


**Advanced Commands (Professional Level)**

Command 12: Firewall Evasion — Fragment Packets
```bash
nmap -sS -f 192.168.1.1
```
Explanation:
- `-f` = Fragment IP packets into 8-byte chunks
- Splits packets to evade simple packet inspection firewalls and older IDS systems
- Can be doubled with -ff for 16-byte fragments
- Use when: Standard scan is being blocked by firewall

Command 13: Firewall Evasion — Decoy Scan
```bash
nmap -sS -D RND:10 192.168.1.1
```
Explanation:
- `-D` = Decoy scan — makes the scan appear to come from multiple sources
- `RND:10` = Generate 10 random decoy IP addresses
- Makes it extremely difficult for the target to identify the real source
- Use when: You need to obscure your scanner's identity during authorized testing

Command 14: Firewall Evasion — Source Port Manipulation
```bash
nmap -sS --source-port 53 192.168.1.1
```
Explanation:
- `--source-port 53` = Set source port to 53 (DNS)
- Many firewalls allow DNS traffic on port 53 without inspection
- Can bypass some firewall rules that allow trusted source ports
- Use when: Firewall is blocking your scan based on source port

Command 15: NSE Vulnerability Scan
```bash
nmap --script vuln 192.168.1.1
```
Explanation:
- `--script vuln` = Run all scripts in the "vuln" category
- Checks for known CVEs, dangerous misconfigurations, and exploitable conditions
- Examples of what it detects: MS17-010 (EternalBlue), Heartbleed, SMB vulnerabilities
- Use when: You need automated vulnerability identification

Command 16: NSE Specific Script
```bash
nmap --script smb-vuln-ms17-010 -p 445 192.168.1.1
```
Explanation:
- `--script smb-vuln-ms17-010` = Run the specific EternalBlue vulnerability check script
- `-p 445` = Only scan port 445 (SMB)
- Reports if the host is vulnerable to MS17-010 (used in WannaCry ransomware)
- Use when: You need to check for a specific known vulnerability

Command 17: Timing Templates
```bash
nmap -T0 192.168.1.1   # Paranoid - extremely slow, evasion-focused
nmap -T1 192.168.1.1   # Sneaky - slow, evasion-focused
nmap -T2 192.168.1.1   # Polite - slow, reduces bandwidth usage
nmap -T3 192.168.1.1   # Normal - default timing
nmap -T4 192.168.1.1   # Aggressive - faster, assumes reliable network
nmap -T5 192.168.1.1   # Insane - extremely fast, may miss results
```
Explanation:
| Template | Speed | Detection Risk | Use Case |
|---|---|---|---|
| T0 | Extremely slow | Very Low | Evading advanced IDS |
| T1 | Very slow | Low | Evading most IDS |
| T2 | Slow | Low | Quiet internal audits |
| T3 | Normal | Medium | Default operations |
| T4 | Fast | High | CTFs, lab environments |
| T5 | Extremely fast | Very High | Speed over accuracy |

Command 18: Full Professional Reconnaissance Command
```bash
nmap -sS -sV -O -A -p- -T4 --script=default,vuln \
     --open -oA /reports/full_recon 192.168.1.0/24
```
Explanation:
- `-sS` = SYN stealth scan
- `-sV` = Version detection
- `-O` = OS detection
- `-A` = Aggressive (traceroute, default scripts)
- `-p-` = All 65535 ports
- `-T4` = Aggressive timing
- `--script=default,vuln` = Run default and vulnerability scripts
- `--open` = Only show open ports
- `-oA /reports/full_recon` = Save all output formats
- `192.168.1.0/24` = Full target subnet
- Use when: Performing a comprehensive network penetration test


## Result Analysis

**Severity Classification of Findings:**
```
CRITICAL  →  Direct exploitation possible (e.g., backdoored service, unpatched RCE)
HIGH      →  Known CVE with available exploit, dangerous misconfiguration
MEDIUM    →  Information disclosure, service version exposure, weak configs
LOW       →  Unnecessary services running, minor information leakage
INFO      →  Open port confirmed, service identified (baseline data)
```

**CVE Research Workflow from Nmap Results:**
```bash
# After identifying: vsftpd 2.3.4
# Research process:

1. Note: vsftpd 2.3.4
2. Search: https://nvd.nist.gov/vuln/search → "vsftpd 2.3.4"
3. Found: CVE-2011-2523 — CVSS Score: 10.0 (Critical)
4. Verify: Check Exploit-DB → https://www.exploit-db.com
5. Cross-reference: Metasploit module availability
   msf> search vsftpd
```

**Common Misconfiguration Findings:**

| Finding | Risk | What It Means |
|---|---|---|
| Anonymous FTP enabled | High | Files accessible without credentials |
| SMB port 445 open | Critical | Check for EternalBlue, SMBGhost |
| Telnet (23) open | High | Cleartext credential transmission |
| SNMP (161/UDP) open | Medium-High | Community string exposure |
| RDP (3389) open | High | Brute force, BlueKeep exposure |
| Default ports open | Medium | Default credentials may work |
| HTTP on non-standard port | Medium | Hidden administrative interfaces |


## Use Cases

**Penetration Testing:**
A penetration tester begins every engagement with Nmap. During the reconnaissance and scanning phases, they use Nmap to build a complete picture of the target network — identifying live hosts, open ports, running services, software versions, and operating systems. This intelligence directly feeds into exploitation planning.

**Vulnerability Assessment:**
Nmap's scripting engine (NSE) contains hundreds of scripts that can detect known vulnerabilities, misconfigured services, weak credentials, and dangerous exposures. Security assessors use Nmap to rapidly identify these weaknesses across entire subnets.

**SOC Monitoring / Network Auditing:**
SOC engineers and network security analysts run scheduled Nmap scans to baseline their network state. Any deviation from the baseline — a new open port, a new host, a changed service — becomes an alert for investigation.

**Incident Response:**
During an active incident, IR teams use Nmap to quickly understand what is live on a network segment, identify rogue devices, map attacker-controlled systems, and understand lateral movement paths.

**Web Application Security Testing:**
Nmap is used to identify web servers, detect HTTP/HTTPS services, enumerate virtual hosts, and identify web technologies before a dedicated web application test begins.

**Compliance Auditing:**
For standards like PCI-DSS, ISO 27001, and HIPAA, Nmap is used to verify that only authorized ports and services are exposed, and that security policies are being enforced correctly.


---

## Additional Information

**Tool Architecture**

How Nmap Works Internally:

Nmap operates by crafting and sending specially constructed network packets to target hosts and analyzing the responses. The entire scanning process is built around the TCP/IP stack behavior of hosts and network devices.
```
┌─────────────────────────────────────────────────────────┐
│                     NMAP ARCHITECTURE                    │
├─────────────────────────────────────────────────────────┤
│                                                          │
│   User Input (CLI / Zenmap)                              │
│         │                                                │
│         ▼                                                │
│   Target Specification & Option Parsing                  │
│         │                                                │
│         ▼                                                │
│   Host Discovery Engine  ─────────────────────────────┐  │
│   (Ping Sweeps, ARP, ICMP, TCP/UDP probes)            │  │
│         │                                              │  │
│         ▼                                              │  │
│   Port Scanning Engine ───────────────────────────────┤  │
│   (SYN, Connect, UDP, FIN, NULL, XMAS, ACK, etc.)    │  │
│         │                                              │  │
│         ▼                                              │  │
│   Service/Version Detection Engine                    │  │
│   (Banner grabbing, Protocol analysis)                │  │
│         │                                              │  │
│         ▼                                              │  │
│   OS Detection Engine                                 │  │
│   (TCP/IP stack fingerprinting)                       │  │
│         │                                              │  │
│         ▼                                              │  │
│   Nmap Scripting Engine (NSE)                         │  │
│   (Lua scripts for advanced detection)                │  │
│         │                                              │  │
│         ▼                                              │  │
│   Output Engine                                       │  │
│   (Normal, XML, Grepable, JSON, Script kiddie)        │  │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

Main Components:

1. Host Discovery Engine
Determines which hosts are alive before port scanning. Uses techniques including ICMP echo requests, TCP SYN to port 443, TCP ACK to port 80, and ICMP timestamp requests. On local networks, ARP requests are used as they cannot be blocked.

2. Port Scanning Engine
The core of Nmap. Sends crafted packets to target ports and determines state based on response behavior:
- Open: Service is actively listening and accepting connections
- Closed: Port is accessible but no service is listening
- Filtered: Firewall or filter is blocking the port — no response or ICMP unreachable
- Unfiltered: Port is accessible but state cannot be determined
- Open|Filtered: Nmap cannot determine if open or filtered
- Closed|Filtered: Nmap cannot determine if closed or filtered

3. Service and Version Detection Engine
After identifying open ports, Nmap sends probe packets and analyzes responses against its nmap-service-probes database (over 11,000 entries) to identify the service name, version, and additional information.

4. OS Detection Engine
Uses TCP/IP stack fingerprinting — every operating system implements the TCP/IP stack slightly differently (window sizes, TTL values, TCP options, IP ID behavior). Nmap compares observed behavior against its nmap-os-db database containing thousands of fingerprints.

5. Nmap Scripting Engine (NSE)
A Lua-based scripting framework that extends Nmap with powerful capabilities. Scripts are categorized as: auth, broadcast, brute, default, discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, vuln.

6. Output Engine
Produces results in multiple formats suitable for manual review, automated processing, and integration with other tools.


**User Manual Style Explanation**

Interface Overview:
Nmap is primarily a command-line tool, but also ships with Zenmap, a cross-platform GUI. The CLI is preferred by professionals for scripting, automation, and precision control.

Basic Syntax Structure:
```
nmap [Scan Type] [Options] {target specification}
```

Target Specification Options:
```
Single IP:        nmap 192.168.1.1
Hostname:         nmap scanme.nmap.org
IP Range:         nmap 192.168.1.1-254
CIDR Notation:    nmap 192.168.1.0/24
Multiple Targets: nmap 192.168.1.1 192.168.1.5
Target List File: nmap -iL targets.txt
Exclude Host:     nmap 192.168.1.0/24 --exclude 192.168.1.1
```

Important Configuration Options:
| Category | Purpose |
|---|---|
| Scan Types | Define HOW packets are sent |
| Port Options | Define WHICH ports to scan |
| Timing Options | Define HOW FAST to scan |
| Output Options | Define HOW results are saved |
| NSE Scripts | Define WHAT additional checks to run |

Typical Professional Workflow:

1. Host discovery to identify live hosts
2. Service scan on discovered hosts
3. Version detection on open ports
4. OS detection
5. NSE script execution for vulnerability checks
6. Output to file for reporting


**GUI Usage (Zenmap)**

Zenmap is the official graphical interface for Nmap, designed to make Nmap accessible while preserving all professional functionality.

Dashboard Overview:
```
┌──────────────────────────────────────────────────────────────┐
│  Target: [192.168.1.0/24    ]  Profile: [Intense Scan ▼]    │
│                                          [Scan]  [Cancel]    │
├──────────────┬───────────────────────────────────────────────┤
│   HOSTS      │  Nmap Output │ Ports/Hosts │ Topology │ ...  │
│              │                                               │
│ 192.168.1.1  │  Starting Nmap 7.95...                       │
│ 192.168.1.5  │  Nmap scan report for 192.168.1.1            │
│ 192.168.1.10 │  Host is up (0.0012s latency)                │
│ 192.168.1.22 │  PORT    STATE  SERVICE  VERSION             │
│              │  22/tcp  open   ssh      OpenSSH 8.2         │
│              │  80/tcp  open   http     Apache 2.4.41       │
└──────────────┴───────────────────────────────────────────────┘
```

Scan Profiles in Zenmap:
| Profile | Equivalent Command | Use Case |
|---|---|---|
| Intense Scan | `nmap -T4 -A -v` | Comprehensive host scan |
| Intense Scan + UDP | `nmap -sS -sU -T4 -A -v` | TCP and UDP together |
| Intense Scan, All TCP Ports | `nmap -p 1-65535 -T4 -A -v` | All ports |
| Ping Scan | `nmap -sn` | Host discovery only |
| Quick Scan | `nmap -T4 -F` | Top 100 ports fast |
| Regular Scan | `nmap` | Default scan |
| Slow Comprehensive Scan | `nmap -sS -sU -T4 -A -v -PE -PP -PS80,443 -PA3389 -PU40125 -PY -g 53 --script "default or (discovery and safe)"` | Full professional audit |

Topology View:
Zenmap includes a network topology visualizer that displays discovered hosts as an interactive graph, showing network relationships and communication paths — extremely useful for network mapping during engagements.

Step-by-Step Zenmap Workflow:

1. Enter target IP or range in Target field
2. Select appropriate scan profile or type custom command
3. Click Scan to begin
4. Monitor live output in the Nmap Output tab
5. Review hosts in the Hosts panel (left sidebar)
6. Analyze port/service details in Ports/Hosts tab
7. Visualize network relationships in Topology tab
8. Save results: Scan → Save Scan


**Professional Workflow**

Real-World Penetration Testing Workflow Using Nmap:

Phase 1: Scoping and Initial Host Discovery
```bash
# Step 1: Identify live hosts in target scope
nmap -sn 10.10.10.0/24 -oA phase1_discovery

# Step 2: Review live hosts
grep "Up" phase1_discovery.gnmap | awk '{print $2}' > live_hosts.txt

# Step 3: Confirm target list with client scope document
```

Phase 2: Port Scanning
```bash
# Step 4: Fast scan of top 1000 ports on all live hosts
nmap -sS -T4 -iL live_hosts.txt -oA phase2_quick_scan

# Step 5: Full port scan on interesting hosts
nmap -sS -p- -T4 10.10.10.5 10.10.10.10 -oA phase2_full_ports
```

Phase 3: Service Enumeration
```bash
# Step 6: Version detection on all open ports
nmap -sV -sC -p $(grep open phase2_full_ports.gnmap | \
  grep -oP '\d+/open' | cut -d/ -f1 | tr '\n' ',') \
  10.10.10.5 -oA phase3_services
```

Phase 4: OS and Advanced Detection
```bash
# Step 7: OS fingerprinting
nmap -O --osscan-guess 10.10.10.5 -oA phase4_os

# Step 8: Aggressive scan with NSE default scripts
nmap -A -sV 10.10.10.5 -oA phase4_aggressive
```

Phase 5: Vulnerability Identification
```bash
# Step 9: Run vulnerability scripts
nmap --script vuln 10.10.10.5 -oA phase5_vulns

# Step 10: Run targeted scripts based on discovered services
# If SMB is open:
nmap --script smb-vuln* -p 445 10.10.10.5

# If HTTP is open:
nmap --script http-* -p 80,443 10.10.10.5

# If FTP is open:
nmap --script ftp-* -p 21 10.10.10.5
```

Phase 6: Documentation
```bash
# Step 11: Convert XML output to HTML report
xsltproc phase5_vulns.xml -o final_report.html

# Step 12: Feed XML into vulnerability management tools
# Import into Metasploit, OpenVAS, or reporting platforms
```


**Output Understanding**

Sample Nmap Output:
```
Starting Nmap 7.95 ( https://nmap.org ) at 2024-01-15 14:30 UTC
Nmap scan report for 192.168.1.10
Host is up (0.0023s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE    SERVICE     VERSION
21/tcp   open     ftp         vsftpd 2.3.4
22/tcp   open     ssh         OpenSSH 7.4 (protocol 2.0)
80/tcp   open     http        Apache httpd 2.4.6
443/tcp  open     ssl/https   Apache httpd 2.4.6
3306/tcp open     mysql       MySQL 5.5.62
8080/tcp filtered http-proxy
MAC Address: 00:0C:29:AA:BB:CC (VMware)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
Network Distance: 1 hop
Service Info: OS: Unix

TRACEROUTE
HOP RTT     ADDRESS
1   2.31 ms 192.168.1.10

Nmap done: 1 IP address (1 host up) scanned in 14.32 seconds
```

Reading the Output — Field by Field:

| Field | Meaning | Security Relevance |
|---|---|---|
| `Host is up (0.0023s latency)` | Host responded to probes; latency in seconds | Higher latency may indicate IDS throttling |
| `PORT` | Port number and protocol | Attack surface identification |
| `STATE` | open/closed/filtered | Open = service running; filtered = firewall present |
| `SERVICE` | Service name from nmap-services database | Identifies what is running |
| `VERSION` | Detected software and version | Critical for CVE lookup |
| `MAC Address` | Hardware MAC and vendor | Identifies device type and manufacturer |
| `OS details` | Fingerprinted operating system | Narrows exploitation options |
| `Network Distance` | Number of hops to target | 1 hop = same network segment |

Identifying Vulnerabilities from Output:

Looking at the sample output, a trained professional immediately identifies:
- `vsftpd 2.3.4` → **Critical:** This version has a backdoor vulnerability (CVE-2011-2523) — connecting to port 6200 after a smiley face login triggers a root shell
- `MySQL 5.5.62` → **High:** End-of-life version with multiple known CVEs
- `OpenSSH 7.4` → **Medium:** Several vulnerabilities depending on configuration
- `Apache httpd 2.4.6` → **High:** Very old version, numerous CVEs
- `8080/tcp filtered` → **Interesting:** Something is on 8080 but filtered — worth further investigation

Detecting False Positives:
- Version detection accuracy depends on banner grabbing — some services return misleading banners
- Always verify version findings manually when high severity CVEs are involved
- Use `--version-intensity 9` for maximum accuracy
- Cross-reference with other enumeration tools (Netcat, curl, Nikto)


**Reporting**

Vulnerability Documentation from Nmap Results:

A professional security report entry derived from Nmap findings:

```
FINDING: Outdated FTP Service with Known Backdoor
Severity: CRITICAL
CVE: CVE-2011-2523
CVSS Score: 10.0

Host: 192.168.1.10
Port: 21/tcp
Service: vsftpd 2.3.4

Description:
The target host is running vsftpd version 2.3.4, which contains a deliberately
planted backdoor. When a username containing a smiley face ":)" is submitted,
the backdoor opens a root command shell on TCP port 6200, providing unauthenticated
root-level access to the system.

Evidence:
[Nmap output showing: 21/tcp open ftp vsftpd 2.3.4]

Risk:
A remote unauthenticated attacker can gain complete root-level access to the
system. This can lead to full system compromise, data theft, lateral movement
across the network, and ransomware deployment.

Remediation:
1. Immediately upgrade vsftpd to version 3.0.5 or latest stable release
2. If FTP is not required, disable and remove the service entirely
3. Consider replacing FTP with SFTP (SSH File Transfer Protocol)
4. Implement network-level controls to restrict FTP access to authorized IPs only
5. Audit the system for signs of compromise

References:
- CVE-2011-2523: https://nvd.nist.gov/vuln/detail/CVE-2011-2523
- Exploit-DB: https://www.exploit-db.com/exploits/17491
```

Report Sections Populated by Nmap:

- Executive Summary: Network exposure overview, total open ports, critical services
- Network Topology: Host inventory from discovery scans
- Attack Surface: All open ports and services across all hosts
- Vulnerability Findings: Detailed entries per vulnerability detected
- Risk Matrix: Severity distribution of all findings
- Remediation Roadmap: Priority-ordered remediation steps


**Advanced Features**

Nmap Scripting Engine (NSE) — Deep Dive:
NSE gives Nmap the power of a vulnerability scanner. Scripts are written in Lua and located at `/usr/share/nmap/scripts/`.

```bash
# List all available scripts
ls /usr/share/nmap/scripts/

# Search for scripts by keyword
nmap --script-help "*smb*"
ls /usr/share/nmap/scripts/ | grep http

# Update NSE script database
nmap --script-updatedb

# Run scripts by category
nmap --script auth 192.168.1.1          # Authentication bypass checks
nmap --script brute 192.168.1.1         # Brute force credentials
nmap --script discovery 192.168.1.1     # Extended discovery
nmap --script exploit 192.168.1.1       # Attempt known exploits
nmap --script malware 192.168.1.1       # Malware detection
nmap --script safe 192.168.1.1          # Safe, non-intrusive scripts
nmap --script vuln 192.168.1.1          # Vulnerability checks
```

Powerful NSE Script Examples:
```bash
# Check for Heartbleed (CVE-2014-0160)
nmap --script ssl-heartbleed -p 443 192.168.1.1

# Check for EternalBlue (MS17-010)
nmap --script smb-vuln-ms17-010 -p 445 192.168.1.1

# HTTP directory enumeration
nmap --script http-enum -p 80 192.168.1.1

# DNS zone transfer attempt
nmap --script dns-zone-transfer --script-args dns-zone-transfer.domain=target.com -p 53 192.168.1.1

# MySQL brute force
nmap --script mysql-brute -p 3306 192.168.1.1

# SNMP community string enumeration
nmap -sU --script snmp-brute -p 161 192.168.1.1

# SSH brute force
nmap --script ssh-brute -p 22 192.168.1.1

# FTP anonymous login check
nmap --script ftp-anon -p 21 192.168.1.1

# Detect WAF presence
nmap --script http-waf-detect -p 80,443 192.168.1.1
```

Automation with Nmap:
```bash
# Automated discovery-to-scan pipeline (Bash)
#!/bin/bash
echo "[*] Running host discovery..."
nmap -sn 192.168.1.0/24 -oG - | grep "Up" | awk '{print $2}' > live.txt

echo "[*] Running full port scan on live hosts..."
nmap -sS -sV -O -p- -T4 -iL live.txt -oA full_scan

echo "[*] Running vulnerability scripts..."
nmap --script vuln -iL live.txt -oA vuln_scan

echo "[*] Generating HTML report..."
xsltproc full_scan.xml -o report.html

echo "[+] Done. Report saved to report.html"
```

Integration with Other Tools:
```bash
# Export to Metasploit
# In Metasploit console:
db_nmap -sV -A 192.168.1.0/24
# Or import existing XML:
db_import /path/to/scan.xml

# Feed to OpenVAS
# Export Nmap XML and import via OpenVAS GSM interface

# Process with custom Python
python3 nmap_parser.py scan.xml

# Use python-nmap library
pip install python-nmap
```

```python
# python-nmap example
import nmap

nm = nmap.PortScanner()
nm.scan('192.168.1.1', '22-443', '-sV')

for host in nm.all_hosts():
    print(f"Host: {host} ({nm[host].hostname()})")
    for proto in nm[host].all_protocols():
        ports = nm[host][proto].keys()
        for port in ports:
            state = nm[host][proto][port]['state']
            service = nm[host][proto][port]['name']
            version = nm[host][proto][port]['version']
            print(f"  Port {port}/{proto}: {state} | {service} {version}")
```


**Best Practices**

How Security Experts Use Nmap Efficiently:

1. Always start with host discovery before port scanning.
Scanning ports on dead hosts wastes enormous time. Always map live hosts first with `-sn`.

2. Use output flags on every scan.
Never run a scan without saving output. Data lost from the terminal is gone forever.
```bash
nmap [options] target -oA scan_$(date +%Y%m%d_%H%M%S)
```

3. Scan UDP services — they're almost always forgotten.
Most assessors only scan TCP. SNMP (161), DNS (53), TFTP (69), and NTP (123) are UDP services that often contain critical vulnerabilities. Always include `-sU` on comprehensive assessments.

4. Use appropriate timing for the environment.
- Production networks: T2 or T3 to avoid disruption
- Lab/CTF environments: T4 or T5 for speed
- Evasion scenarios: T1 or T0

Avoiding Detection During Testing:
```bash
# Randomize host order to avoid sequential scanning detection
nmap --randomize-hosts 192.168.1.0/24

# Add random delays between probes
nmap --scan-delay 1s 192.168.1.1

# Use source IP spoofing (requires proper routing)
nmap -S 192.168.1.100 192.168.1.1

# Spoof MAC address
nmap --spoof-mac 0 192.168.1.1    # Random MAC
nmap --spoof-mac Cisco 192.168.1.1  # Cisco vendor MAC

# Reduce packet send rate
nmap --max-rate 10 192.168.1.1    # 10 packets per second maximum
```

Common Beginner Mistakes:

| Mistake | Consequence | Fix |
|---|---|---|
| Not saving output | Losing all scan data | Always use `-oA` |
| Only scanning TCP | Missing UDP services | Add `-sU` to assessments |
| Using T5 on production | Crashing services, DoS conditions | Use T2-T3 on production |
| Not running as root | SYN scan fails, falls back to connect | Use `sudo` for raw packet scans |
| Forgetting `-p-` | Missing services on non-standard ports | Use `-p-` for thorough assessments |
| Running vuln scripts without permission | Legal and ethical violations | Always have written authorization |
| Not updating NSE scripts | Missing latest vulnerability checks | Run `nmap --script-updatedb` regularly |


**Related Tools**

Professional Tool Combinations:

```
TOOL CHAIN 1 — Full Network Penetration Test:
Nmap → Metasploit → Meterpreter → Mimikatz
(Discovery) → (Exploitation) → (Post-Exploitation) → (Credential Dumping)

TOOL CHAIN 2 — Vulnerability Assessment:
Nmap → OpenVAS/Nessus → Exploit-DB Research → Reporting
(Service Mapping) → (Deep Vulnerability Scanning) → (Validation) → (Report)

TOOL CHAIN 3 — Web Application Testing:
Nmap → Nikto → Burp Suite → SQLmap
(Port Discovery) → (Web Enumeration) → (Manual Testing) → (SQL Injection)

TOOL CHAIN 4 — SOC/Blue Team Auditing:
Nmap → Wireshark → Zeek → Splunk
(Network Mapping) → (Traffic Analysis) → (Protocol Analysis) → (Log Correlation)

TOOL CHAIN 5 — OSINT to Exploitation:
Shodan → Nmap → Searchsploit → Metasploit
(Passive Recon) → (Active Scanning) → (Exploit Search) → (Exploitation)
```

Tool Reference Table:

| Tool | Relationship to Nmap | Use Case |
|---|---|---|
| Metasploit | Receives Nmap XML import | Exploitation after scanning |
| Nessus/OpenVAS | More detailed vuln scanning | Deep vulnerability assessment |
| Masscan | Faster large-scale port scanning | Internet-wide scanning |
| Wireshark | Captures packets Nmap generates | Traffic analysis and verification |
| Netcat | Manual service interaction | Verify Nmap findings manually |
| Nikto | Web-specific scanning | After Nmap identifies HTTP services |
| Burp Suite | Web app proxy | After web services are identified |
| Shodan | Passive version of Nmap | OSINT before active scanning |
| Enum4Linux | SMB enumeration | After Nmap finds 445/139 open |
| Gobuster/Dirb | Web directory brute-force | After Nmap identifies web servers |


**Interview Questions**

Q1: What is the difference between a SYN scan (-sS) and a TCP Connect scan (-sT)?
Answer:
A SYN scan sends a SYN packet and never completes the three-way handshake — it sends RST after receiving SYN-ACK. This means the connection never fully establishes, so it typically won't appear in application-level logs. It requires root/administrator privileges because it crafts raw packets. A TCP Connect scan completes the full three-way handshake using the OS network stack — it does not require elevated privileges but is more visible, appearing in application logs as completed connections. SYN scan is preferred in professional testing for its speed and relative stealth.

Q2: What does a "filtered" port state mean in Nmap, and how is it different from "closed"?
Answer:
A filtered port means Nmap cannot determine if the port is open because packet filtering (a firewall, ACL, or host-based filter) is preventing probes from reaching the port. Nmap receives no response or receives an ICMP unreachable message. A closed port is actively accessible — the target host responds with a TCP RST — meaning no service is listening but the port is not blocked. Filtered is more concerning from an assessment perspective because it indicates active security controls and potentially hidden services behind the filter.

Q3: How would you perform a scan that avoids detection by an IDS?
Answer:
Several techniques can reduce detection probability: Use `-T1` or `T0` timing to space out packets over long periods. Use `-f` to fragment packets, breaking them into chunks that simple signature detection may miss. Use `--scan-delay` to add time between probes. Use `-D` decoy scanning to generate noise from multiple fake source IPs. Use `--source-port 53` or `--source-port 80` to spoof trusted source ports. Randomize host order with `--randomize-hosts`. Use `--spoof-mac` to impersonate trusted devices. None of these guarantee evasion against modern IDS/IPS, but they significantly increase complexity for defenders.

Q4: What is the Nmap Scripting Engine (NSE) and how is it used in professional assessments?
Answer:
NSE is a Lua-based scripting framework built into Nmap that extends its functionality beyond basic port scanning. It allows professionals to run automated checks against discovered services for vulnerability detection, authentication testing, brute force attacks, malware detection, and information gathering. Scripts are organized by category: auth, brute, default, discovery, exploit, safe, vuln, and others. In professional assessments, NSE is used to automate the detection of known CVEs (like MS17-010, Heartbleed), check for misconfigurations, enumerate service details, and attempt default credential testing — all integrated within the same scanning workflow.

Q5: How do you scan for UDP services with Nmap, and why is this important?
Answer:
UDP scanning is performed with the `-sU` flag: `nmap -sU 192.168.1.1`. UDP scanning is slower than TCP because when a UDP port is closed, the host returns an ICMP port unreachable message, but when it is open, there is often no response, so Nmap must wait for timeouts. It is critically important because many dangerous services run on UDP — SNMP on 161 (community string exposure, network device enumeration), DNS on 53 (zone transfer vulnerabilities), NTP on 123 (amplification attacks), TFTP on 69 (unauthenticated file access). These services are routinely missed when only TCP is scanned, creating blind spots in security assessments.

Q6: You run an Nmap scan and see vsftpd 2.3.4 on port 21. What do you do next?
Answer:
vsftpd 2.3.4 is a famous compromised version containing a deliberately planted backdoor (CVE-2011-2523, CVSS 10.0). The next steps would be: First, verify the version by manually banner-grabbing with Netcat (`nc 192.168.1.1 21`) to confirm Nmap's detection. Second, confirm the CVE and lookup available exploit modules (Metasploit has module `exploit/unix/ftp/vsftpd_234_backdoor`). Third, document the finding with evidence, severity, and CVE reference. Fourth, if this is an authorized pentest, attempt exploitation to demonstrate impact. Fifth, provide remediation guidance: immediately upgrade vsftpd, consider replacing FTP with SFTP, implement network controls restricting access to the service.

Q7: What is OS fingerprinting in Nmap and how does it work?
Answer:
OS fingerprinting uses the `-O` flag and works by analyzing how a host's TCP/IP stack responds to a series of specially crafted probes. Every operating system implements the TCP/IP stack slightly differently — variations include TCP window sizes, TTL values, IP ID field behavior, TCP options like SACK and timestamps, response to malformed packets, and ICMP response behavior. Nmap sends up to 16 different probe packets and compares the response patterns against its `nmap-os-db` database, which contains thousands of known OS fingerprints. The result includes the OS name, version, confidence percentage, and CPE (Common Platform Enumeration) identifier. Accuracy depends on the host having at least one open and one closed port available.

Q8: What is the difference between Nmap and Masscan, and when would you use each?
Answer:
Nmap is a comprehensive network scanner with service detection, OS fingerprinting, NSE scripting, and precise results — it is the standard for thorough security assessments. Masscan is an ultra-high-speed port scanner designed for internet-wide scanning, capable of scanning the entire internet in under 6 minutes with its asynchronous packet engine. However, Masscan provides only basic port state information without service detection or scripting capabilities. In professional practice, they are often combined: use Masscan for rapid port discovery across large IP ranges (internet-scale or large enterprise networks), then feed the discovered open ports into Nmap for precise service detection and vulnerability checking. This dramatically reduces total scan time while maintaining assessment quality.

Q9: How would you use Nmap in a SOC or blue team context?
Answer:
In a SOC/blue team context, Nmap is used for several defensive purposes: Network baselining — running regular Nmap scans to establish what is the known-good state of the network, documenting all live hosts, open ports, and running services. Change detection — comparing scan results over time using Ndiff (`ndiff scan_old.xml scan_new.xml`) to identify new devices, newly opened ports, or changed service versions. Security policy verification — confirming that firewall rules are enforced correctly and only authorized services are accessible. Asset inventory — maintaining an up-to-date record of all networked assets. Pre-assessment preparation — understanding the network before deploying IDS/IPS sensors or conducting threat hunting activities.

Q10: What legal and ethical considerations must be taken before running Nmap against a network?
Answer:
This is critically important. Running Nmap against a network without explicit written authorization is illegal in most jurisdictions — it can violate the Computer Fraud and Abuse Act (CFAA) in the US, the Computer Misuse Act in the UK, and equivalent laws worldwide. Before running any scan, you must have: Written authorization from the asset owner, clearly defining the scope of permitted targets, permitted scan types, and the time window for testing. A Rules of Engagement (ROE) document signed by both parties. Confirmation that the scope does not include third-party infrastructure (cloud providers, ISPs, other tenants in shared environments) without their separate authorization. Even in CTF (Capture the Flag) environments, scanning should only target designated IP ranges. In professional penetration testing, the written authorization letter must be accessible during the engagement in case of law enforcement inquiry.
