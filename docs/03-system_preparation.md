# SambaLAB — System Preparation

## Overview

This stage prepares `DC01` for deployment as the Samba Active Directory Domain Controller. The system was updated, required administration and troubleshooting utilities were installed, and time synchronization was verified.

## System Information

| Setting          | Value                                            |
| ---------------- | ------------------------------------------------ |
| Hostname         | `dc01`                                           |
| Operating System | Ubuntu 26.04 LTS                                 |
| Architecture     | ARM64                                            |
| Virtualization   | QEMU / UTM                                       |
| Role             | Planned Samba Active Directory Domain Controller |

## Package Updates

The system package repositories were updated and installed packages were upgraded before deploying Samba.

```bash
sudo apt update
sudo apt upgrade -y
```

## Administrative Utilities

The following utilities were installed for system administration, networking, troubleshooting, and Samba/AD management:

```bash
sudo apt install -y \
  vim \
  curl \
  wget \
  net-tools \
  dnsutils \
  chrony \
  acl \
  attr
```

### Purpose

| Package     | Purpose                                 |
| ----------- | --------------------------------------- |
| `vim`       | Configuration file editing              |
| `curl`      | Network testing and HTTP requests       |
| `wget`      | File retrieval                          |
| `net-tools` | Legacy network administration utilities |
| `dnsutils`  | DNS troubleshooting tools such as `dig` |
| `chrony`    | Network Time Protocol synchronization   |
| `acl`       | Access Control List management          |
| `attr`      | Extended filesystem attributes          |

## Time Synchronization

Accurate time is important for Active Directory and Kerberos authentication. `chrony` was installed and verified.

The system reports:

```text
System clock synchronized: yes
NTP service: active
Time zone: America/Phoenix (MST, -0700)
```

Chrony verification:

```bash
chronyc tracking
```

The system was synchronized with a Canonical NTP server and reported a normal leap status.

## Verification

Time synchronization was verified using:

```bash
timedatectl
chronyc tracking
```

Both checks confirmed that the system clock is synchronized and the NTP service is active.

## Status

| Component                    | Status   |
| ---------------------------- | -------- |
| System updated               | Complete |
| Administration utilities     | Complete |
| Chrony/NTP                   | Complete |
| Time synchronization         | Verified |
| Ready for Samba installation | Yes      |
