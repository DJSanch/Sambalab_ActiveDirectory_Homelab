# SambaLAB — Kerberos Authentication

## Overview

Kerberos is the primary authentication protocol used by Active Directory. Samba provides a Kerberos Key Distribution Center (KDC) as part of the Samba Active Directory Domain Controller.

The SambaLAB Kerberos realm is:

```text
SAMBALAB.LOCAL
```

The Kerberos service is provided by:

```text
dc01.sambalab.local
```

## Kerberos Configuration

| Setting                 | Value                 |
| ----------------------- | --------------------- |
| Kerberos Realm          | `SAMBALAB.LOCAL`      |
| Active Directory Domain | `sambalab.local`      |
| KDC                     | `dc01.sambalab.local` |
| Kerberos Port           | `88`                  |
| Administrative Account  | `Administrator`       |

Samba generated the Active Directory Kerberos configuration during domain provisioning.

The Samba-generated configuration is located at:

```text
/var/lib/samba/private/krb5.conf
```

The configuration specifies:

```text
[libdefaults]
    default_realm = SAMBALAB.LOCAL
    dns_lookup_realm = false
    dns_lookup_kdc = true
```

This allows Kerberos to use DNS service records to locate the KDC for the `SAMBALAB.LOCAL` realm.

## Obtaining a Kerberos Ticket

Kerberos authentication was tested using the domain Administrator account:

```bash id="5c8v6m"
kinit Administrator@SAMBALAB.LOCAL
```

The command completed successfully after entering the Administrator password.

The obtained Kerberos ticket was verified with:

```bash id="7t2p1n"
klist
```

The result showed:

```text id="z0d7k1"
Credentials cache: FILE:/tmp/krb5cc_1000
Principal: Administrator@SAMBALAB.LOCAL

Issued                Expires               Principal
Aug 28 11:28:39 2026  Aug 28 21:28:39 2026  krbtgt/SAMBALAB.LOCAL@SAMBALAB.LOCAL
```

![Kerberos Authentication Verification](screenshots/kerberos-kinit-klist.png)

*Figure 1 — Successful Kerberos authentication using the SambaLAB Administrator account and verification of the Kerberos ticket with `klist`.*

## Kerberos Ticket

The ticket obtained from the KDC is a Ticket Granting Ticket (TGT):

```text
krbtgt/SAMBALAB.LOCAL@SAMBALAB.LOCAL
```

The TGT allows the authenticated user to request service tickets for other services within the Active Directory environment without repeatedly providing their password.

The successful ticket acquisition confirms that:

* The Samba KDC is operational.
* The `SAMBALAB.LOCAL` Kerberos realm is functioning.
* DNS can locate the Kerberos service.
* The Administrator account can authenticate against Active Directory.
* Kerberos ticket issuance is working correctly.

## Ticket Management

The current Kerberos ticket can be removed from the local credential cache using:

```bash id="q0b9m2"
kdestroy
```

The ticket can then be reacquired with:

```bash id="e7c3n8"
kinit Administrator@SAMBALAB.LOCAL
```

The ticket cache can be inspected again with:

```bash id="m5p1z4"
klist
```

## Authentication Flow

The basic Kerberos authentication process in the SambaLAB environment is:

```text
Administrator
      │
      │ kinit
      ▼
DC01 / Kerberos KDC
      │
      │ Authentication
      ▼
Ticket Granting Ticket (TGT)
      │
      ▼
krbtgt/SAMBALAB.LOCAL
      │
      │ Request service ticket
      ▼
Active Directory Services
```

## Verification Status

| Test                    | Result                |
| ----------------------- | --------------------- |
| Kerberos realm          | `SAMBALAB.LOCAL`      |
| KDC                     | `dc01.sambalab.local` |
| `kinit` authentication  | Successful            |
| TGT issued              | Successful            |
| `klist` verification    | Successful            |
| Kerberos authentication | Operational           |

Kerberos authentication is successfully operational within the SambaLAB Active Directory environment.
