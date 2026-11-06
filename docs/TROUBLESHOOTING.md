# Troubleshooting Notes

## HSRP / OSPF restoration packet loss

**Observed:** DSW1 uplink failure caused about one ping loss. Restoring the link previously caused about 8.4 pings of loss.

**Cause recorded in project notes:** HSRP preemption allowed DSW1 to reclaim the active gateway before the OSPF route to the branch was ready, briefly creating a routing black hole.

**Change tested:** Configure the R-HQ-to-distribution OSPF links as point-to-point. The notes report restoration loss decreasing from about 8.4 to about 4.0 pings (approximately 52% improvement).

**Caveat:** Packet Tracer did not expose an HSRP preempt delay setting in the tested environment. The measured timings are simulation-specific.

## ACL test appears to fail

- Confirm the source PC has the expected IP address and default gateway.
- Confirm the destination server is powered on and addressed correctly.
- Check the ACL with `show access-lists`.
- Check which SVI/interface the ACL is attached to and whether the direction is correct.
- Remember ACL rules are processed top to bottom and an implicit deny exists at the end.

## SSH does not connect

- Confirm the target device has a reachable management IP.
- Confirm SSH is configured and the VTY lines accept SSH.
- Confirm the VTY access-class permits the Admin PC source IP.
- Confirm local username/password credentials match the simulation.
- Telnet being disabled is intentional; use SSH.

## VPN traffic does not pass

- Check reachability between the VPN peer outside addresses.
- Check that the interesting-traffic ACLs mirror each other.
- Check IKE/IPsec security association status and packet counters.
- Verify pre-shared keys match without publishing them.
- Confirm OSPF or static routing provides the required return path.

These are diagnostic suggestions, not confirmation that a specific fault exists in the supplied file.
