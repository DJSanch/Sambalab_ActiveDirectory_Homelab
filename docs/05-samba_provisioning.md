# SambaLAB — Samba AD Provisioning

## Overview

The Samba Active Directory Domain Controller was provisioned on `DC01` using Samba's built-in domain provisioning tools. The domain was configured as `sambalab.local` with the NetBIOS domain name `SAMBALAB`.

The provisioning process created the Active Directory database, SYSVOL, NETLOGON, Kerberos configuration, and Samba DNS components required for the domain controller.

## Active Directory Domain Configuration

| Setting           | Value                              |
| ----------------- | ---------------------------------- |
| Domain            | `sambalab.local`                   |
| Kerberos Realm    | `SAMBALAB.LOCAL`                   |
| NetBIOS Domain    | `SAMBALAB`                         |
| Domain Controller | `DC01`                             |
| FQDN              | `dc01.sambalab.local`              |
| IPv4              | `192.168.0.200`                    |
| Server Role       | Active Directory Domain Controller |

## Domain Provisioning

The Samba domain was provisioned using the Samba Active Directory provisioning utility.

The resulting configuration was validated with:

```bash
sudo testparm -s
```

The configuration reported:

```text
Server role: ROLE_ACTIVE_DIRECTORY_DC
```

This confirms that Samba is configured to operate as an Active Directory Domain Controller.

## Domain Verification

The provisioned domain was verified using:

```bash
sudo samba-tool domain info dc01.sambalab.local
```

The command confirmed the following:

```text
Forest           : sambalab.local
Domain           : sambalab.local
Netbios domain   : SAMBALAB
DC name          : dc01.sambalab.local
DC netbios name  : DC01
Server site      : Default-First-Site-Name
Client site      : Default-First-Site-Name
```

![Samba AD Domain Verification](screenshots/samba-ad-domain-verification.png)

*Figure 1 — Successful verification of the Samba Active Directory domain and Domain Controller.*

## Domain and Forest Functional Level

The current domain and forest functional levels were verified with:

```bash
sudo samba-tool domain level show
```

The lab currently uses:

```text
Forest function level: (Windows) 2008 R2
Domain function level: (Windows) 2008 R2
Lowest function level of a DC: (Windows) 2008 R2
```

These settings provide the compatibility baseline for the current SambaLAB Active Directory environment.

## Provisioning Status

| Component                 | Status           |
| ------------------------- | ---------------- |
| Active Directory database | Provisioned      |
| Domain                    | `sambalab.local` |
| Kerberos Realm            | `SAMBALAB.LOCAL` |
| NetBIOS Domain            | `SAMBALAB`       |
| Domain Controller         | `DC01`           |
| SYSVOL                    | Created          |
| NETLOGON                  | Created          |
| Samba AD DC service       | Running          |
| Domain verification       | Successful       |

The SambaLAB Active Directory domain is successfully provisioned and ready for the next configuration stages, including **Active Directory structure, DNS, Kerberos authentication, users, groups, and client domain joining**.
