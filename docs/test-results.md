# Test Results and Observations

This document records the project results supplied with the project brief. Timings are approximate simulation observations, not guaranteed real-world performance figures.

## Security validation

The project records **13 out of 13 security tests passed**. The demo guide lists representative reachability checks for Engineering, Guests, Admin, and Branch users.

A passing ping is evidence of reachability for that test; a failed ping is evidence that the tested ICMP path did not succeed. These checks alone do not constitute a complete security audit.

## HSRP / routing failover observations

| Scenario | Failure packet loss | Restoration packet loss |
|---|---:|---:|
| DSW1 uplink failure, before tuning | ~1 ping | ~8.4 pings |
| DSW1 uplink failure, after tuning | ~1 ping | ~4.0 pings |
| Complete DSW1 shutdown test | ~1 ping | ~4.5 pings on restoration |

The recorded restoration loss decreased from approximately 8.4 to 4.0 pings, an improvement of about 52%.

## Observed issue

When the DSW1-to-R-HQ link was restored, HSRP preemption allowed DSW1 to reclaim the active gateway role before the OSPF route to the branch was fully ready. This temporarily created a routing black hole and increased packet loss during restoration.

## Change tested

The R-HQ-to-distribution-switch OSPF links were configured as point-to-point links. The project notes report that this avoided DR/BDR election on those links and helped OSPF become ready sooner.

After the change, the recorded restoration loss reduced to approximately 4.0 pings.

## Interpretation and limitations

- The change improved the measured restoration behavior in this Packet Tracer simulation.
- Failure behavior remained approximately one lost ping in the recorded uplink test.
- Full DSW1 shutdown and link-restoration tests produced slightly different results.
- Packet Tracer may model protocol timing differently from physical Cisco devices.
- Results should be reproduced from the supplied `.pkt` file before being quoted as final measurements in a formal report.
