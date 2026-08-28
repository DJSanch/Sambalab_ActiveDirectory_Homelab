# SambaLAB — Active Directory Structure & Organization

## Overview

The Samba Active Directory domain uses the standard Active Directory organizational structure created during domain provisioning.

The current domain is:

```text
SAMBALAB.LOCAL
```

The Domain Controller is:

```text
DC01
```

## Current Active Directory Structure

The current SambaLAB environment contains the default Active Directory organizational units, users, and security groups created during domain provisioning.

### Organizational Units

The current OU structure contains:

```text
SAMBALAB.LOCAL
│
└── OU=Domain Controllers
    └── DC01
```

The `Domain Controllers` OU contains the computer account for the Samba Domain Controller.

The OU structure can be expanded later as the lab develops to include organizational departments such as IT, HR, and Sales.

### Users

The following user accounts currently exist:

```text
Users
├── Administrator
├── Guest
└── krbtgt
```

`Administrator` is the primary administrative account used to manage the Samba Active Directory environment.

`Guest` is the default guest account.

`krbtgt` is the Kerberos service account used by Active Directory for Kerberos authentication.

### Groups

Samba created the standard Active Directory security and system groups during domain provisioning.

Important groups currently present include:

```text
Domain Admins
Domain Users
Domain Computers
Domain Controllers
Enterprise Admins
Schema Admins
Administrators
Backup Operators
Server Operators
Account Operators
DnsAdmins
DnsUpdateProxy
Protected Users
Guests
Users
```

These groups provide the foundation for assigning administrative privileges, user permissions, computer membership, and other security roles within the domain.

## Active Directory Hierarchy

The current environment can be represented as:

```text
SAMBALAB.LOCAL
│
├── Domain Controllers
│   └── DC01
│
├── Users
│   ├── Administrator
│   ├── Guest
│   └── krbtgt
│
└── Built-in Groups
    ├── Domain Admins
    ├── Domain Users
    ├── Domain Computers
    ├── Domain Controllers
    ├── Enterprise Admins
    ├── Schema Admins
    ├── DnsAdmins
    └── Other default security groups
```

## Verification

The Active Directory structure was verified using Samba administrative commands:

```bash
sudo samba-tool ou list
sudo samba-tool user list
sudo samba-tool group list
```

The commands confirmed the presence of the default Domain Controllers OU, built-in user accounts, and standard Active Directory groups.

![Active Directory Structure Verification](screenshots/active-directory-structure.png)

*Figure 1 — Verification of the current SambaLAB organizational units, user accounts, and Active Directory groups.*

## LDAP Database Verification

The Active Directory LDAP database was also validated using `samba-tool ldapcmp`.

The comparison successfully validated the following directory contexts:

```text
DOMAIN
CONFIGURATION
SCHEMA
DNSDOMAIN
DNSFOREST
```

The successful comparisons confirm that the Samba Active Directory directory database and its associated directory partitions are functioning correctly.

## Future Organization

As the SambaLAB environment develops, additional organizational units and accounts can be created to simulate a real business environment.

A future structure may include:

```text
SAMBALAB.LOCAL
│
├── Domain Controllers
│   └── DC01
│
├── IT
│   ├── IT-Admins
│   └── IT-Users
│
├── HR
│   └── HR-Users
│
└── Sales
    └── Sales-Users
```

These custom OUs will be created and documented only when they are implemented in the lab.

## Current Status

| Component                 | Status           |
| ------------------------- | ---------------- |
| Domain                    | `sambalab.local` |
| Domain Controller         | `DC01`           |
| Domain Controllers OU     | Present          |
| Administrator account     | Present          |
| Guest account             | Present          |
| Kerberos `krbtgt` account | Present          |
| Default security groups   | Present          |
| Custom departmental OUs   | Not yet created  |
| LDAP directory            | Operational      |
