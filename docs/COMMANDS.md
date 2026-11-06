# Cisco CLI Command Reference

This is a quick reference for understanding and demonstrating the project. Commands that depend on the device or interface are examples; confirm the exact target before running them.

## Enter configuration mode

```text
enable
configure terminal
hostname DEVICE-NAME
end
write memory
```

- `enable`: enter privileged EXEC mode.
- `configure terminal`: enter global configuration mode.
- `hostname`: set the device name shown in the CLI prompt.
- `end`: return to privileged EXEC mode.
- `write memory`: save the running configuration.

## Inspect configuration and status

```text
show running-config
show ip interface brief
show vlan brief
show interfaces trunk
show ip route
show ip ospf neighbor
show standby brief
show access-lists
show ip nat translations
show crypto isakmp sa
show crypto ipsec sa
```

Some commands vary by Packet Tracer device model and IOS feature support.

## VLANs and trunks

```text
vlan 10
 name ENGINEERING
!
interface <PORT>
 switchport mode access
 switchport access vlan 10
!
interface <UPLINK>
 switchport mode trunk
```

A VLAN separates a Layer-2 broadcast domain. An access port normally carries one VLAN; a trunk carries multiple VLANs between network devices.

## ACL concepts

```text
access-list 99 permit host 192.168.99.10
```

This standard ACL permits the Admin PC's source address. Standard ACLs have an implicit deny at the end. For VTY management restriction, the ACL must be applied to the VTY lines, for example:

```text
line vty 0 4
 access-class 99 in
 transport input ssh
```

`access-class 99 in` filters incoming remote terminal sessions by source. `transport input ssh` permits SSH and excludes Telnet on those VTY lines. Verify the actual line range and ACL on each device.

Extended ACL rule order matters. A deny-all rule at the end implements the documented default-deny policy, but the preceding permit/deny rules and their direction must be checked carefully.

## SSH

From a Packet Tracer PC command prompt, the documented demo pattern is:

```text
ssh -l admin 192.168.99.2
```

Then enter the password configured in the local simulation. Exit with:

```text
exit
```

SSH encrypts remote CLI traffic. Telnet does not encrypt the session and should not be used for this project's device administration.

## HSRP

```text
show standby brief
```

HSRP provides a shared virtual default gateway. The project design uses `.1` as the virtual gateway, DSW1 priority 105, and DSW2 priority 100. DSW1 tracks its HQ-router uplink; when the tracked link fails, its priority falls to 95 so DSW2 can take over.

## OSPF

```text
show ip ospf neighbor
show ip route ospf
```

OSPF dynamically exchanges routing information. The project notes record that the HQ router-to-distribution links were configured as point-to-point to improve restoration convergence in the Packet Tracer test.

## NAT/PAT

```text
show ip nat translations
show ip nat statistics
```

NAT/PAT allows multiple private internal hosts to share a translated outside address. The project notes record `203.0.113.2` as the simulated public address.

## VPN

```text
show crypto isakmp sa
show crypto ipsec sa
```

These commands can help inspect IPsec negotiation and packet counters on supported IOS images. The project notes specify AES-256, SHA, and pre-shared-key authentication. Never place the real key in a public repository.

## Connectivity tests

```text
ping 192.168.20.10
ping 192.168.30.10
ping 8.8.8.8
tracert 8.8.8.8
```

Use the test matrix in [`TEST-CASES.md`](TEST-CASES.md) to interpret expected allow/deny outcomes.
