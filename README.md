# Cyber Intelligence & Threat Analysis Lab

An isolated, educational cybersecurity lab for practicing network monitoring,
ethical hacking, digital forensics, threat intelligence, and SOC workflows.
All testing must remain inside systems and networks that you own or are
explicitly authorized to test.

## Current status

The Phase 1 base lab is operational:

- **OWASP Juice Shop**: vulnerable training application at `172.30.0.10`
- **Ubuntu target**: training host at `172.30.0.20`
- **Docker network**: `cyber-labnet`, internal subnet `172.30.0.0/24`
- **Experiment record**: [docs/experiments/EXP-001.md](docs/experiments/EXP-001.md)

The vulnerable application has no published host port. It is reachable only
from containers attached to the internal lab network.

## Repository layout

```text
.
├── lab/
│   └── docker-compose.yml       # Isolated training containers and network
├── sensors/                     # Planned Suricata, Zeek, and Wazuh setup
├── backend/                     # Planned events and incidents API
├── dashboard/                   # Planned SOC dashboard
├── docs/
│   └── experiments/             # Reproducible lab experiment records
└── blockchain-forensics/docs/   # Project guides and reference diagrams
```

## Requirements

- Docker Engine
- Docker Compose v2
- Sufficient disk space for the Juice Shop and Ubuntu images

Kali Linux, Wireshark, Suricata, Zeek, and Wazuh will be added progressively.
Do not install every component at once; start with the smallest working lab.

## Start the lab

From the project root:

```bash
docker compose -f lab/docker-compose.yml up -d
docker compose -f lab/docker-compose.yml ps
```

Verify the internal network and container addresses:

```bash
docker network inspect cyber-labnet
```

Verify Juice Shop from the Ubuntu target:

```bash
docker exec cyber-lab-ubuntu-target \
  bash -lc "exec 3<>/dev/tcp/172.30.0.10/3000; \
  printf 'GET / HTTP/1.0\r\nHost: juice-shop\r\n\r\n' >&3; \
  head -n 1 <&3"
```

The expected response begins with `HTTP/1.1 200 OK`.

Stop the lab when finished:

```bash
docker compose -f lab/docker-compose.yml down
```

## Safe operating rules

1. Test only authorized lab systems.
2. Keep vulnerable applications on the internal Docker network.
3. Never publish Juice Shop to `0.0.0.0` or expose it to the internet.
4. Use synthetic accounts and test data, never real credentials.
5. Do not run denial-of-service, flooding, or destructive experiments.
6. Preserve original evidence and record SHA-256 hashes before analysis.
7. Keep confirmed facts separate from hypotheses in investigation reports.
8. Do not upload private files or sensitive data to public analysis services.

## Learning path

1. **Foundations:** Linux, TCP/IP, DNS, HTTP, Docker isolation.
2. **Scanning and packets:** authorized Nmap exercises and Wireshark/tcpdump.
3. **Web security:** Juice Shop challenges and remediation reports.
4. **Monitoring:** Suricata alerts, Zeek metadata, and later Wazuh events.
5. **Forensics and intelligence:** evidence handling, IOC management, and
   documented incident timelines.
6. **SOC platform:** PostgreSQL, event ingestion, incident correlation, and a
   Next.js dashboard.
7. **Optional blockchain forensics:** public transaction analysis with clear
   limits on attribution.

See the detailed guides in
[blockchain-forensics/docs/](blockchain-forensics/docs/) for the full
12-week plan and the project architecture.