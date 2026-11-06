# SENTINELNET
### A Zero-Trust Network Design for a Semiconductor / Chip-Design Company

SENTINELNET is a Cisco Packet Tracer networking project that demonstrates how a company can separate users, engineering systems, confidential intellectual property, guests, and a branch office using VLAN segmentation, access-control lists (ACLs), secure administration, dynamic routing, redundancy, NAT, and an IPsec site-to-site VPN.

The central idea is **default deny**: being connected to the company network does not automatically grant access to every system.

> **Project artifact:** `packet-tracer/SentinelNet.pkt`

---

## What this project demonstrates

- **Network segmentation:** separate VLANs for Engineering, EDA servers, the IP vault, Guests, and Management.
- **Least-privilege access:** ACLs allow only the traffic required by each role.
- **Secure device administration:** SSH is used for remote management; Telnet is disabled; management access is restricted to the Admin PC.
- **High availability:** HSRP provides a virtual default gateway across two distribution switches.
- **Loop prevention:** Rapid Spanning Tree Protocol (Rapid STP) prevents Layer 2 switching loops.
- **Dynamic routing:** OSPF exchanges routes between the HQ and branch network.
- **Internet access:** NAT/PAT allows private internal addresses to share a public address.
- **Branch connectivity:** an IPsec site-to-site VPN protects traffic between HQ and the branch.

## Network policy at a glance

| Source | Destination | Expected result | Reason |
|---|---|---:|---|
| Engineering | EDA servers | Allow | Engineers need design tools |
| Engineering | IP vault | Deny | Restrict confidential IP |
| Guests | Internet | Allow | Guest internet access |
| Guests | Internal networks | Deny | Isolate guest devices |
| Branch PCs | EDA servers | Allow | Access through the VPN |
| Branch PCs | IP vault | Deny | Keep vault access restricted |
| Admin PC | Vault administration | Allow | Authorized administration |
| Admin PC | Network-device management | Allow via SSH | Secure administration |
| Other PCs | Network-device management | Deny | Restrict management plane |
| Any unlisted traffic | Any | Deny | Default-deny policy |

See [`docs/security-policy.md`](docs/security-policy.md) for the policy details.

## Logical network plan

The project uses the following VLAN and subnet plan:

| VLAN | Purpose | Subnet |
|---:|---|---|
| 10 | Engineering | `192.168.10.0/24` |
| 20 | EDA servers | `192.168.20.0/24` |
| 30 | IP vault | `192.168.30.0/24` |
| 40 | Guests | `192.168.40.0/24` |
| 99 | Network management | `192.168.99.0/24` |

The HQ distribution switches provide inter-VLAN routing and the VLAN gateway services. The branch connects to HQ through the site-to-site VPN.

For the device roles and topology overview, see [`docs/architecture.md`](docs/architecture.md).

## High-availability design

DSW1 and DSW2 use HSRP to provide a shared virtual gateway (`.1`) for the VLANs. DSW1 is preferred as the active device, while DSW2 is the standby device. Tracking the DSW1-to-HQ-router link lowers DSW1's HSRP priority when that link fails, allowing DSW2 to take over.

Rapid STP prevents switching loops. The redundant ASW1-to-DSW2 path is expected to be blocked when necessary by spanning tree; it is a backup path, not a broken connection.

## Validation results

The project notes record the following results:

| Validation | Before tuning | After tuning |
|---|---:|---:|
| DSW1 uplink failure: packet loss | ~1 ping | ~1 ping |
| DSW1 uplink restoration: packet loss | ~8.4 pings | ~4.0 pings |
| Improvement in restoration loss | — | ~52% |
| Full DSW1 shutdown: packet loss | — | ~1 ping on failure; ~4.5 on restoration |
| Security checks | — | 13/13 passed |

The restoration improvement was achieved by configuring the HQ-router-to-distribution-switch OSPF links as point-to-point. This reduced routing convergence overhead in the Packet Tracer simulation. The result is specific to the tested simulation and should not be interpreted as a universal production-network guarantee.

See [`docs/test-results.md`](docs/test-results.md) for the recorded test scenarios and caveats.

## Repository structure

```text
SentinelNet/
├── packet-tracer/
│   └── SentinelNet.pkt
├── docs/
│   ├── architecture.md
│   ├── security-policy.md
│   ├── demo-guide.md
│   └── test-results.md
├── screenshots/
│   └── README.md
├── .gitignore
└── README.md
```

## Open the simulation

1. Install Cisco Packet Tracer.
2. Clone or download this repository.
3. Open `packet-tracer/SentinelNet.pkt` in Packet Tracer.
4. Inspect the topology and run the validation commands in [`docs/demo-guide.md`](docs/demo-guide.md).

The `.pkt` file is the simulation artifact. The Markdown documents explain the intended design and the tests recorded for this project.

## Important before making the repository public

- Review the Packet Tracer file for embedded device credentials, pre-shared VPN keys, or other secrets.
- Change any demonstration passwords and VPN keys before publishing or presenting the project.
- Do not commit real company data, private keys, production IPs, or confidential network diagrams.
- Add screenshots of the topology and successful/blocked tests to `screenshots/` if you have them.
- Confirm that your course/team permits public release of the simulation file.

**Credential hygiene:** passwords are intentionally not reproduced in this repository. Use the credentials configured in your local simulation, and replace any weak/demo credentials before sharing.

## Scope and disclaimer

This is an educational network simulation, not a production-ready deployment blueprint. Packet Tracer behavior, convergence timings, and supported protocol features may differ from real Cisco hardware and software. Validate all configurations against the target platform before adapting this design to a live network.
