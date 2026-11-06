# Architecture Overview

## 1. Objective

SENTINELNET models a semiconductor/chip-design company network. Its systems have different trust levels: engineering workstations need access to EDA tools, confidential chip-design files must remain protected, guests should only reach the internet, and a branch office needs controlled access to selected HQ services.

The architecture uses segmentation and explicit policy enforcement rather than treating the internal network as trusted by default.

## 2. Main device roles

| Device | Role |
|---|---|
| ASW1 | HQ access switch for Engineering, Guest, and Admin endpoints |
| ASW2 | HQ access switch for EDA and IP-vault servers |
| DSW1 / DSW2 | Redundant HQ multilayer distribution switches; inter-VLAN routing, gateway services, DHCP, and ACL enforcement |
| R-HQ | HQ edge router; ISP connectivity, NAT, and VPN termination |
| ISP | Simulated internet/ISP network; `8.8.8.8` is used as an internet-reachability test destination |
| R-BR | Branch router; participates in WAN/VPN connectivity |
| S-BR | Branch access switch |
| BR-PC1 / BR-PC2 | Branch user endpoints |

## 3. VLAN and IP addressing

| VLAN | Name / purpose | Subnet |
|---:|---|---|
| 10 | Engineering | `192.168.10.0/24` |
| 20 | EDA servers | `192.168.20.0/24` |
| 30 | IP vault | `192.168.30.0/24` |
| 40 | Guests | `192.168.40.0/24` |
| 99 | Management | `192.168.99.0/24` |

The design uses VLANs to create separate Layer 2 broadcast domains. Trunk links carry multiple VLANs between switches. The distribution layer performs inter-VLAN routing and applies access policy.

## 4. Redundancy and Layer 2 control

- DSW1 and DSW2 use HSRP to present a shared virtual default gateway (`.1`) to clients.
- DSW1 is preferred with priority 105; DSW2 uses priority 100.
- Tracking the DSW1-to-R-HQ link reduces DSW1's priority to 95 when the tracked link fails, allowing DSW2 to become active.
- Rapid STP prevents Layer 2 loops. DSW1 is the intended root bridge.
- The redundant ASW1-to-DSW2 connection may be placed into a spanning-tree blocking state while the primary path is available.

## 5. Routing and WAN connectivity

- OSPF dynamically advertises and learns routes between the HQ and branch routing domains.
- R-HQ connects the HQ network to the simulated ISP.
- NAT/PAT translates private `192.168.x.x` addresses to the simulated public address `203.0.113.2`.
- An IPsec site-to-site VPN protects traffic between HQ and the branch. The project notes specify AES-256, SHA, and pre-shared-key authentication.

## 6. DHCP

The distribution switches provide DHCP for the HQ VLANs. The recorded split-scope arrangement is:

- DSW1: `.11` through `.127`
- DSW2: `.128` through `.254`

This is intended to avoid handing out the same client addresses from both distribution switches.

## 7. Security enforcement points

- VLAN ACLs restrict traffic between user and server segments.
- ACL policy is mirrored on DSW1 and DSW2 so the policy remains consistent after an HSRP role change.
- The management plane is restricted to the Admin PC.
- SSH is the permitted remote-management protocol; Telnet is disabled.
- ACLs end with a deny-all rule to implement the default-deny approach.

For the intended allow/deny matrix, see [`security-policy.md`](security-policy.md).
