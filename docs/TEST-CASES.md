# Security Test Cases

The project brief records 13/13 security checks passed. The individual names/results for all 13 checks were not supplied, so this file lists the documented representative tests and does not invent the remaining test records.

| Test | Run from | Command | Expected |
|---|---|---|---|
| Engineering to EDA | Eng-PC | `ping 192.168.20.10` | Allow |
| Engineering to vault | Eng-PC | `ping 192.168.30.10` | Deny |
| Guest to EDA | Guest-Laptop | `ping 192.168.20.10` | Deny |
| Guest to vault | Guest-Laptop | `ping 192.168.30.10` | Deny |
| Guest to Admin PC | Guest-Laptop | `ping 192.168.99.10` | Deny |
| Guest to internet test IP | Guest-Laptop | `ping 8.8.8.8` | Allow |
| Admin to vault | Admin-PC | `ping 192.168.30.10` | Allow |
| Branch to EDA | BR-PC1 | `ping 192.168.20.10` | Allow over VPN |
| Branch to vault | BR-PC1 | `ping 192.168.30.10` | Deny |
| Admin SSH to DSW1 | Admin-PC | `ssh -l admin 192.168.99.2` | Allow |
| Admin SSH to R-HQ | Admin-PC | `ssh -l admin 10.0.0.1` | Allow |
| Engineering SSH attempt | Eng-PC | `ssh -l admin 192.168.10.2` | Deny/fail |

## Interpreting results

A blocked ping may appear as `Destination host unreachable` or `Request timed out`. An unauthorized SSH attempt may be refused or time out depending on the Packet Tracer behavior and the exact ACL placement. Record the actual output from the simulation when presenting.

Ping tests validate ICMP reachability only; they do not prove every application protocol is protected.
