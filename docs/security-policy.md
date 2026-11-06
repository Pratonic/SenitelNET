# Security Policy and ACL Design

## Security objective

Apply least privilege: users and devices should receive only the network access required for their role. Traffic not explicitly permitted by policy should be denied.

## Policy matrix

| Source | Destination | Policy |
|---|---|---|
| Engineering VLAN 10 | EDA VLAN 20 | Permit |
| Engineering VLAN 10 | Vault VLAN 30 | Deny |
| Guest VLAN 40 | Internet | Permit |
| Guest VLAN 40 | Internal VLANs | Deny |
| Branch network | EDA VLAN 20 over IPsec VPN | Permit |
| Branch network | Vault VLAN 30 | Deny |
| Admin PC (`192.168.99.10`) | Vault administration | Permit |
| Admin PC (`192.168.99.10`) | Router/switch management over SSH | Permit |
| Non-admin endpoints | Router/switch management | Deny |
| Unmatched traffic | Any destination | Deny |

## What an ACL does

An Access Control List (ACL) is a set of rules that checks traffic against conditions such as source address, destination address, protocol, or port. A matching permit rule allows the traffic; a matching deny rule blocks it. On Cisco devices, ACLs are processed in order, and an implicit deny exists at the end of an ACL. The project design also records an explicit `deny ip any any` at the end of its VLAN ACLs to make the default-deny intent visible.

## Why the policy is applied on both distribution switches

DSW1 and DSW2 can each become the active HSRP gateway. Keeping the corresponding ACL policy on both devices prevents a failover from unintentionally changing the security policy.

## SSH management restriction

SSH provides encrypted remote command-line access to network devices. The intended management policy is:

- Only the Admin PC (`192.168.99.10`) may initiate device-management sessions.
- SSH is allowed on the VTY lines.
- Telnet is not accepted.
- Non-admin endpoints should not be able to remotely administer the switches or routers.

In Cisco IOS, a standard ACL used for VTY access must be applied to the VTY lines with `access-class ... in`; `transport input ssh` limits accepted remote terminal transport to SSH. The precise running configuration should be checked in Packet Tracer before using this document as a configuration reference.

## Demonstration checks

The expected behavior is documented in [`demo-guide.md`](demo-guide.md). Successful and blocked ping tests demonstrate network reachability policy; the SSH tests demonstrate who may administer the network devices.

> **Security note:** ICMP ping results are useful demonstrations, but they do not prove that every application protocol or every possible path is correctly secured. A production design would require broader testing, logging, configuration review, and platform-specific validation.
