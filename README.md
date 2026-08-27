# SambaLAB — Active Directory Homelab

A hands-on IT Systems Administration homelab designed to simulate a small organization's **Active Directory environment** using **Samba Active Directory Domain Controller (AD DC)**.

The purpose of this project is to develop practical experience in identity management, authentication, DNS, networking, Windows domain integration, and system administration.

---

## Table of Contents

* [Environment](#environment)
* [Company Scenario](#company-scenario)
* [Lab Architecture](#lab-architecture)
* [Technologies](#technologies)
* [Objectives](#objectives)
* [Documentation](#documentation)
* [Security Practices](#security-practices)
* [Disclaimer](#disclaimer)

---

## Environment

### Host Machine

| Component        | Specification                   |
| ---------------- | ------------------------------- |
| Device           | MacBook Air        |
| Operating System | MacOS                 |
| Architecture     | ARM64                           |
| Virtualization   | UTM                             |

### Virtual Machine

| Component        | Specification                            |
| ---------------- | ---------------------------------------- |
| VM Name          | `DC01`                                   |
| Operating System | Ubuntu Linux                             |
| Architecture     | ARM64                                    |
| Role             | Samba Active Directory Domain Controller |

### Domain

| Setting           | Value                 |
| ----------------- | --------------------- |
| Domain            | `sambalab.local`      |
| Realm             | `SAMBALAB.LOCAL`      |
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

The environment will include organizational units, security groups, user accounts, computers, servers, and shared resources.

---

## Planned Active Directory Structure

```text
sambalab.local
│
├── Users
│   ├── IT
│   ├── HR
│   ├── Finance
│   └── Sales
│
├── Groups
│   ├── IT-Staff
│   ├── HR-Staff
│   ├── Finance-Staff
│   ├── Sales-Staff
│   ├── IT-Admins
│   ├── Helpdesk
│   ├── Server-Admins
│   └── Network-Admins
│
├── Computers
│   ├── IT
│   ├── HR
│   ├── Finance
│   └── Sales
│
├── Servers
│
└── Admins
```

---

## Lab Architecture

```text
                              SAMBALAB HOMELAB
                                      │
                         ┌────────────┴────────────┐
                         │     MacBook Air M1      │
                         │       macOS / ARM64     │
                         │                         │
                         │          UTM            │
                         └────────────┬────────────┘
                                      │
                           Virtual Network
                                      │
                    ┌─────────────────┴─────────────────┐
                    │                                   │
              ┌─────▼─────┐                       ┌─────▼─────┐
              │   DC01    │                       │  FILE01   │
              │ Ubuntu    │                       │  Ubuntu   │
              │ ARM64     │                       │  ARM64    │
              │           │                       │           │
              │ Samba AD  │                       │ SMB/File  │
              │ DNS       │                       │ Server    │
              │ Kerberos  │                       │           │
              └─────┬─────┘                       └─────┬─────┘
                    │                                   │
                    └─────────────────┬─────────────────┘
                                      │
                              sambalab.local
                                      │
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
        ┌─────▼─────┐           ┌─────▼─────┐          ┌─────▼─────┐
        │    IT     │           │    HR     │          │  Finance  │
        │ 20 Users  │           │ 20 Users  │          │ 20 Users  │
        └───────────┘           └───────────┘          └───────────┘
                                      │
                                ┌─────▼─────┐
                                │   Sales   │
                                │ 20 Users  │
                                └───────────┘
                                      │
                              80 Users Total
                                      │
                    ┌─────────────────┴─────────────────┐
                    │                                   │
              Security Groups                    Organizational Units
                    │                                   │
             ┌──────┴──────┐                     ┌──────┴──────┐
             │             │                     │             │
        Department      Admin Groups          Users       Computers
         Groups
             │
      ┌──────┼──────┐
      │      │      │
     IT     HR   Finance
                    │
                  Sales

                                      │
                                      ▼
                         Windows Domain Clients
                                      │
                  ┌───────────────────┼───────────────────┐
                  │                   │                   │
             Windows 11          Windows 11          Windows 11
                IT                  HR                 Finance
                  │
                  └───────────────────┬───────────────────┘
                                      │
                                Domain Joined
```

Windows clients will be added later to test **domain joining, authentication, Group Policy, permissions, and other Windows administration tasks**.

---

## Technologies

* Ubuntu Linux
* Samba Active Directory Domain Controller
* Kerberos
* DNS
* SMB/CIFS
* Windows 11 Pro
* UTM Virtualization
* Git
* GitHub

---

## Objectives

The primary objectives of SambaLAB are to gain practical experience with:

* Active Directory domain administration
* User and group management
* Organizational Units (OUs)
* DNS administration
* Kerberos authentication
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

---


## 🔐 Security Practices

This project is designed to follow basic system administration and security principles.

* Use separate administrative accounts.
* Apply least-privilege access.
* Use security groups for access management.
* Avoid unnecessary Domain Administrator privileges.
* Do not store passwords or credentials in the repository.
* Do not commit private keys, certificates, or other sensitive information.
* Use fictional users and organizational data.

---

## ⚠️ Disclaimer

SambaLAB is a personal homelab created for educational and professional development purposes.

All users, credentials, organizational information, hostnames, and network configurations are created for laboratory purposes only.
