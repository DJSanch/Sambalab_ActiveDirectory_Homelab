# SambaLAB — LDAP Configuration & Directory Access

## Overview

Samba Active Directory provides an LDAP-compatible directory service for storing and managing domain objects such as users, computers, groups, and organizational units.

The LDAP service is provided by the Samba Active Directory Domain Controller:

```text
dc01.sambalab.local
```

The SambaLAB LDAP directory uses the following base distinguished name:

```text
DC=sambalab,DC=local
```

## LDAP Configuration

| Setting        | Value                  |
| -------------- | ---------------------- |
| LDAP Server    | `dc01.sambalab.local`  |
| LDAP Protocol  | LDAPv3                 |
| LDAP Port      | `389`                  |
| Directory Base | `DC=sambalab,DC=local` |
| Authentication | Kerberos GSSAPI        |
| Domain         | `sambalab.local`       |

The LDAP service is integrated into Samba AD and does not require a separate OpenLDAP server.

## LDAP Directory Structure

The SambaLAB directory is organized using the standard Active Directory naming structure:

```text
DC=sambalab,DC=local
│
├── CN=Users
│   ├── Administrator
│   ├── Guest
│   └── krbtgt
│
├── OU=Domain Controllers
│   └── DC01
│
├── CN=Computers
│
├── CN=Builtin
│
└── CN=Configuration
```

Additional Active Directory containers and DNS partitions are maintained by Samba.

## LDAP Authentication

LDAP access was tested using Kerberos-based GSSAPI authentication.

First, a Kerberos ticket was obtained for the Administrator account:

```bash
kinit Administrator@SAMBALAB.LOCAL
```

The LDAP directory was then queried using:

```bash
ldapsearch -H ldap://dc01.sambalab.local \
  -Y GSSAPI \
  -b "DC=sambalab,DC=local" \
  "(objectClass=user)" sAMAccountName
```

The LDAP client successfully authenticated using the existing Kerberos credentials.

The command returned the user objects stored in the Samba Active Directory database.

![LDAP GSSAPI Authentication and Directory Search](screenshots/ldap-gssapi-search.png)

*Figure 1 — Successful LDAPv3 directory search using Kerberos GSSAPI authentication.*

## LDAP Query Results

The query returned the following user accounts:

```text
krbtgt
Administrator
DC01$
Guest
```

The `DC01$` account represents the Domain Controller's computer account.

The `krbtgt` account is used by the Kerberos authentication infrastructure, while `Administrator` and `Guest` are standard domain user accounts created during domain provisioning.

## SASL/GSSAPI Authentication

The LDAP test reported:

```text
SASL/GSSAPI authentication started
SASL username: Administrator@SAMBALAB.LOCAL
SASL SSF: 256
SASL data security layer installed.
```

This confirms that LDAP successfully used the Kerberos credentials from the local ticket cache.

GSSAPI provides authenticated and protected communication between the LDAP client and the Active Directory LDAP service.

## LDAP and Active Directory Relationship

The relationship between the services can be represented as:

```text
Administrator
      │
      │ Kerberos authentication
      ▼
DC01 / KDC
      │
      │ TGT
      ▼
LDAP Client
      │
      │ GSSAPI
      ▼
DC01 / LDAP
      │
      ▼
SAMBALAB Active Directory
```

Kerberos provides the authentication mechanism, while LDAP provides directory access to Active Directory objects.

## Verification Status

| Test                    | Result                |
| ----------------------- | --------------------- |
| LDAP server             | `dc01.sambalab.local` |
| LDAP protocol           | LDAPv3                |
| LDAP port               | `389`                 |
| Kerberos authentication | Successful            |
| GSSAPI authentication   | Successful            |
| LDAP directory search   | Successful            |
| User objects returned   | Successful            |

The SambaLAB LDAP directory is operational and successfully integrates with Kerberos-based Active Directory authentication.
