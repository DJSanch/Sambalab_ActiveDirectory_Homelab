# SambaLAB — Active Directory Homelab

A hands-on IT Systems Administration homelab designed to simulate a small organization's **Active Directory environment** using **Samba Active Directory Domain Controller (AD DC)**.

The purpose of this project is to develop practical experience in identity management, authentication, DNS, networking, Windows domain integration, security administration, troubleshooting, and Linux system administration.

---

## Table of Contents

* [Environment](#environment)
* [Company Scenario](#company-scenario)
* [Lab Architecture](#lab-architecture)
* [Technologies](#technologies)
* [Implemented Features](#implemented-features)
* [Objectives](#objectives)
* [Documentation](#documentation)
* [Security Practices](#security-practices)
* [Future Expansion](#future-expansion)
* [Disclaimer](#disclaimer)

---

## Environment

### Host Machine

| Component        | Specification |
| ---------------- | ------------- |
| Device           | MacBook Air   |
| Operating System | macOS         |
| Architecture     | ARM64         |
| Virtualization   | UTM           |

### Domain Controller

| Component        | Specification                            |
| ---------------- | ---------------------------------------- |
| VM Name          | `DC01`                                   |
| Operating System | Ubuntu Linux 26.04 LTS                   |
| Architecture     | ARM64                                    |
| Role             | Samba Active Directory Domain Controller |
| IP Address       | `192.168.0.200`                          |

### Active Directory Domain

| Setting           | Value                 |
| ----------------- | --------------------- |
| Domain            | `sambalab.local`      |
| Kerberos Realm    | `SAMBALAB.LOCAL`      |
| NetBIOS Domain    | `SAMBALAB`            |
| Domain Controller | `DC01`                |
| FQDN              | `dc01.sambalab.local` |

---

## Company Scenario

SambaLAB simulates a small organization consisting of four departments.

| Department      |  Users |
| --------------- | -----: |
| IT              |     20 |
| Human Resources |     20 |
| Finance         |     20 |
| Sales           |     20 |
| **Total**       | **80** |

The environment uses Organizational Units (OUs), security groups, user accounts, Group Policy, DNS, Kerberos authentication, and LDAP directory services to simulate common enterprise IT administration tasks.

---

## Lab Architecture

```text
                         SAMBALAB HOMELAB

                              │
                     ┌────────▼────────┐
                     │   MacBook Air   │
                     │    macOS ARM64  │
                     │                 │
                     │      UTM        │
                     └────────┬────────┘
                              │
                       Virtual Network
                              │
                       ┌──────▼──────┐
                       │     DC01    │
                       │ Ubuntu 26.04│
                       │    ARM64    │
                       │             │
                       │  Samba AD   │
                       │  DNS        │
                       │  Kerberos   │
                       │  LDAP       │
                       └──────┬──────┘
                              │
                       sambalab.local
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
     ┌────▼────┐         ┌────▼────┐        ┌────▼────┐
     │   IT    │         │   HR    │        │ Finance │
     │ 20 Users│         │ 20 Users│        │ 20 Users│
     └─────────┘         └─────────┘        └─────────┘
                              │
                         ┌────▼────┐
                         │  Sales  │
                         │ 20 Users│
                         └─────────┘

                         80 Users Total
```

Windows domain clients and additional infrastructure will be added as the homelab is expanded.

---

## Technologies

* Ubuntu Linux 26.04 LTS
* Samba 4.23.6
* Samba Active Directory Domain Controller
* Kerberos
* DNS
* LDAP
* SMB/CIFS
* Windows 11 Pro
* UTM / QEMU virtualization
* Git
* GitHub

---

## Implemented Features

The following components have been successfully configured and tested:

### Active Directory

* Samba Active Directory Domain Controller
* `sambalab.local` domain
* `SAMBALAB` NetBIOS domain
* `DC01` Domain Controller
* Active Directory database
* Domain and forest configuration

### Organizational Structure

Departmental organizational units have been created for:

* IT
* HR
* Finance
* Sales

### User Management

The lab contains **80 departmental user accounts**:

* 20 IT users
* 20 HR users
* 20 Finance users
* 20 Sales users

User accounts were created and verified using `samba-tool`.

### Groups

Departmental and administrative security groups were configured to support role-based access control and future permission management.

### DNS

Samba's internal DNS service was configured and verified.

DNS validation included:

* Domain Controller A record
* Kerberos SRV records
* LDAP SRV records
* Domain name resolution

### Kerberos

Kerberos authentication was successfully configured and tested.

A valid Kerberos Ticket Granting Ticket was obtained using:

```bash
kinit Administrator@SAMBALAB.LOCAL
```

Ticket status was verified using:

```bash
klist
```

### LDAP

LDAP directory access was tested using Kerberos/GSSAPI authentication.

Example:

```bash
ldapsearch -H ldap://dc01.sambalab.local \
  -Y GSSAPI \
  -b "DC=sambalab,DC=local" \
  "(objectClass=user)" sAMAccountName
```

### Group Policy

A custom Group Policy Object was created:

```text
SambaLAB Workstation Security Policy
```

The GPO was successfully linked to the IT organizational unit.

### Password Policy

The domain password policy was configured to enforce stronger authentication requirements, including:

* Password complexity
* Password history
* Minimum password length
* Password expiration
* Account lockout controls
* Failed-login threshold

### Troubleshooting

The lab included troubleshooting and resolution of:

* Samba DNS port conflicts
* `systemd-resolved` conflicts
* DNS resolution failures
* Kerberos KDC connectivity problems
* LDAP authentication issues
* GPO authentication issues

---

## Objectives

The primary objectives of SambaLAB are to gain practical experience with:

* Active Directory domain administration
* User and group management
* Organizational Units (OUs)
* DNS administration
* Kerberos authentication
* LDAP directory services
* Group Policy management
* Windows domain integration
* File and folder permissions
* Access control
* Least-privilege administration
* Administrative delegation
* Linux system administration
* Network troubleshooting
* Windows administration
* Command-line administration
* Infrastructure documentation
* IT security practices

---

## Documentation

Detailed implementation documentation is available in the [`docs/`](docs/) directory.

Documentation includes:

1. System Preparation
2. Samba Installation
3. Active Directory Structure
4. Samba AD Provisioning
5. Organizational Units, Users & Groups
6. Group Policy Configuration
7. Password Policy
8. DNS Configuration and Validation
9. Troubleshooting and Final Verification

Each document contains relevant commands, configuration details, verification results, and selected screenshots from the lab environment.

---

## Security Practices

This project follows basic system administration and security principles.

* Use separate administrative accounts where appropriate.
* Apply least-privilege access.
* Use security groups for access management.
* Avoid unnecessary Domain Administrator privileges.
* Do not store passwords or credentials in the repository.
* Do not commit private keys, certificates, or other sensitive information.
* Use fictional users and organizational data.
* Keep the lab isolated from production systems.
* Document configuration changes and troubleshooting procedures.

---

## Future Expansion

The SambaLAB environment will continue to evolve with additional infrastructure and administration scenarios.

Planned additions include:

* Windows 11 domain clients
* Windows domain joining
* Group Policy testing from Windows clients
* File server integration
* SMB share permissions
* NTFS-style access control scenarios
* Helpdesk administrative delegation
* User account lockout testing
* Password reset workflows
* Additional server roles
* Backup and recovery testing
* Network monitoring
* Centralized logging
* Active Directory troubleshooting scenarios

---

## Project Status

**Current Status: Active Development**

The core Samba Active Directory environment is operational.

The following core services have been successfully configured and verified:

```text
Samba AD DC       ✓
Active Directory  ✓
DNS               ✓
Kerberos          ✓
LDAP              ✓
Users             ✓
Groups            ✓
OUs               ✓
GPO               ✓
Password Policy   ✓
Troubleshooting   ✓
```

The project will continue to expand as additional Windows clients, servers, permissions, and enterprise administration scenarios are added.

---

## Disclaimer

SambaLAB is a personal homelab created for educational and professional development purposes.

All users, credentials, organizational information, hostnames, and network configurations are created for laboratory purposes only.

This environment is not intended for production use.
