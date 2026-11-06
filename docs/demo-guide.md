# Demo and Validation Guide

This guide follows the demonstration flow recorded for the project. Run the commands from the relevant Packet Tracer PC command prompt.

## 1. ACL: Engineering access

From **Eng-PC**:

```text
ping 192.168.20.10
ping 192.168.30.10
```

Expected:
- EDA server (`192.168.20.10`): reachable.
- IP vault (`192.168.30.10`): blocked.

Explain: Engineering needs EDA tools, but should not have unrestricted access to confidential chip-design IP.

## 2. ACL: Guest isolation

From **Guest-Laptop**:

```text
ping 192.168.20.10
ping 192.168.30.10
ping 192.168.99.10
ping 8.8.8.8
```

Expected:
- EDA server: blocked.
- IP vault: blocked.
- Admin PC: blocked.
- Internet test destination: reachable.

Explain: Guests receive internet access without being allowed to access internal company systems.

## 3. ACL: Admin access

From **Admin-PC**:

```text
ping 192.168.30.10
```

Expected: reachable, subject to the configured vault policy.

Explain: The Admin PC is the designated management endpoint and is allowed to perform the relevant administrative access.

## 4. ACL: Branch access through VPN

From **BR-PC1**:

```text
ping 192.168.20.10
ping 192.168.30.10
```

Expected:
- EDA server: reachable through the HQ-to-branch VPN.
- IP vault: blocked.

Explain: The VPN protects branch-to-HQ traffic in transit, while ACLs still restrict which internal resources the branch can access. Encryption does not mean unrestricted authorization.

## 5. SSH: Authorized management

From **Admin-PC**:

```text
ssh -l admin 192.168.99.2
```

Log in using the credentials configured in the local Packet Tracer file. After verifying the device prompt, leave the session:

```text
exit
```

Then test the HQ router:

```text
ssh -l admin 10.0.0.1
```

Log in using the local simulation credentials, then:

```text
exit
```

Expected: the Admin PC can establish SSH sessions to the configured management targets.

## 6. SSH: Unauthorized management attempt

From **Eng-PC**, attempt the documented management test:

```text
ssh -l admin 192.168.10.2
```

Expected: the session should be rejected or fail to establish. Packet Tracer may display a refusal, timeout, or another failure message depending on the exact configuration and simulation behavior.

Explain: ordinary engineering endpoints are not authorized to administer network infrastructure.

## 7. Useful explanation for the presentation

- **ACL:** decides which traffic is allowed to reach a destination.
- **SSH:** provides encrypted remote administration of routers and switches.
- **VTY lines:** the virtual terminal access lines used for remote CLI sessions.
- **Default deny:** anything not explicitly permitted is blocked.
- **VPN:** encrypts traffic between the branch and HQ; ACLs still control access after the traffic arrives.

## Demo safety

Do not publish the actual configured passwords or VPN pre-shared key. Use the local simulation's credentials during a private demonstration and rotate any demonstration secrets before public sharing.
