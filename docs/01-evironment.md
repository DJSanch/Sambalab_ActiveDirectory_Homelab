# SambaLAB — Environment

## Host Machine

| Component        | Specification  |
| ---------------- | -------------- |
| Device           | MacBook Air M1 |
| Operating System | macOS          |
| Architecture     | ARM64          |
| Virtualization   | UTM            |

## Virtual Machine

| Component        | Specification                            |
| ---------------- | ---------------------------------------- |
| VM Name          | DC01                                     |
| Operating System | Ubuntu 26.04 LTS                         |
| Architecture     | ARM64                                    |
| Role             | Samba Active Directory Domain Controller |
| virtualization   | QEMU                                     |

## Domain

| Setting           | Value                 |
| ----------------- | --------------------- |
| Domain            | `sambalab.local`      |
| Realm             | `SAMBALAB.LOCAL`      |
| NetBIOS Domain    | `SAMBALAB`            |
| Domain Controller | `DC01`                |
| FQDN              | `dc01.sambalab.local` |

## Network Environment

| Component         | Value                 |
|-------------------|-----------------------|
| Interface         | `enp0s1`              |
| IPv4 Address      | `192.168.0.104/24`    |
| Network           | `192.168.0.0/24`      |
| Default Gateway   | `192.168.0.1`         |
| Address Assignment| DHCP                  |
| MAC Address       | `5a:27:72:79:08:25`   |
| DNS               | DHCP-provided         |

## Purpose

This environment will be used to deploy and manage the SambaLAB Active Directory infrastructure and provide a controlled environment for practicing system administration tasks.
