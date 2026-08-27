# SambaLAB — Network Configuration

## 1. Network Overview

DC01 is connected to the home LAN through the UTM virtual network.

| Setting         | Value                                          |
| --------------- | ---------------------------------------------- |
| Interface       | `enp0s1`                                       |
| IPv4 Address    | `192.168.0.200/24`                             |
| Network         | `192.168.0.0/24`                               |
| Default Gateway | `192.168.0.1`                                  |
| IPv4 Assignment | Static                                         |
| DNS             | `68.105.28.11`, `68.105.29.11`, `68.105.28.12` |
| MAC Address     | `5A:27:72:79:08:25`                            |
| Network Manager | NetworkManager                                 |

## 2. Initial Configuration

DC01 initially received its IPv4 address through DHCP:

```text
192.168.0.104/24
```

The Domain Controller requires a predictable IP address because Active Directory services depend heavily on reliable DNS and network addressing.

Because access to the physical router was unavailable, a DHCP reservation could not be configured. A static IPv4 address was therefore configured directly on DC01.

The selected address was:

```text
192.168.0.200/24
```

Initial connectivity testing showed that `.200`, `.201`, and `.202` did not respond to ICMP. `192.168.0.200` was selected for DC01.

> Note: The router's DHCP pool could not be independently verified because router administration was unavailable. The selected address should therefore be monitored for potential IP conflicts.

## 3. Netplan Configuration

The Ubuntu installation contained multiple Netplan configuration files.

The original installer configuration enabled DHCP:

```yaml
dhcp4: true
dhcp6: true
```

The NetworkManager-generated configuration also contained DHCP settings while a static address had been added.

This resulted in DC01 temporarily having two IPv4 addresses:

```text
192.168.0.200/24  → Static
192.168.0.104/24  → DHCP
```

The duplicate configuration was identified and corrected.

The original installer configuration was backed up and disabled:

```text
00-installer-config.yaml.disabled
```

A clean NetworkManager-based Netplan configuration was then created for DC01.

## 4. Final IPv4 Configuration

```text
Interface: enp0s1
Address:   192.168.0.200/24
Gateway:   192.168.0.1
Method:    Static
```

IPv6 remains enabled through DHCP and has not been modified.

## 5. Network Verification

### Routing

```bash
ip route
```

Expected result:

```text
default via 192.168.0.1 dev enp0s1
192.168.0.0/24 dev enp0s1 src 192.168.0.200
```

### DNS Configuration

```bash
resolvectl status
```

The system successfully reported the configured DNS servers.

### Gateway Connectivity

```bash
ping -c 4 192.168.0.1
```

Result:

```text
4 packets transmitted
4 received
0% packet loss
```

### DNS and Internet Connectivity

```bash
ping -c 4 google.com
```

Result:

```text
4 packets transmitted
4 received
0% packet loss
```

DNS resolution successfully translated `google.com` to an IPv6 address.

## 6. Final Network State

```text
                    Home LAN
                 192.168.0.0/24
                        │
                        │
                 192.168.0.1
                    Gateway
                        │
                        │
                192.168.0.200
                     DC01
                        │
                  enp0s1
                        │
                Ubuntu 26.04 LTS
```

## 7. Status

**Network configuration:**  Complete

**Static IPv4:**  Configured

**Gateway connectivity:**  Verified

**DNS resolution:**  Verified

**Internet connectivity:**  Verified


