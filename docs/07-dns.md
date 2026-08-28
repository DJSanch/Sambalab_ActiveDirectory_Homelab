# SambaLAB — DNS Configuration

## Overview

Samba Active Directory provides an integrated DNS server for the `SAMBALAB.LOCAL` domain.

DNS is a critical component of the Active Directory environment because domain controllers, Kerberos authentication, LDAP services, and domain-joined clients rely on DNS records to locate required services.

The Samba DNS server is running directly through the `samba-ad-dc` service on `DC01`.

## DNS Configuration

| Setting       | Value                 |
| ------------- | --------------------- |
| DNS Domain    | `sambalab.local`      |
| DNS Server    | `dc01.sambalab.local` |
| DNS Server IP | `192.168.0.200`       |
| DNS Service   | Samba Internal DNS    |
| Kerberos Port | `88`                  |
| LDAP Port     | `389`                 |
| DNS Port      | `53`                  |

The Samba DNS service listens on port `53` and provides DNS records for the Active Directory domain.

## DNS Records

The Domain Controller's A record was verified with:

```bash
host -t A dc01.sambalab.local
```

Result:

```text
dc01.sambalab.local has address 192.168.0.200
```

This confirms that the Domain Controller hostname resolves to the correct static IPv4 address.

## Kerberos Service Record

The Kerberos SRV record was verified with:

```bash
host -t SRV _kerberos._udp.sambalab.local
```

Result:

```text
_kerberos._udp.sambalab.local has SRV record 0 100 88 dc01.sambalab.local.
```

This allows clients to locate the Kerberos Key Distribution Center (KDC) provided by the Domain Controller.

## LDAP Service Record

The LDAP SRV record was verified with:

```bash
host -t SRV _ldap._tcp.sambalab.local
```

Result:

```text
_ldap._tcp.sambalab.local has SRV record 0 100 389 dc01.sambalab.local.
```

This allows Active Directory clients and services to locate the LDAP service provided by the Domain Controller.

![Samba AD DNS Verification](screenshots/dns-verification.png)

*Figure 1 — Successful verification of the Domain Controller A record and Active Directory Kerberos and LDAP SRV records.*

## DNS Service Verification

The Samba DNS listener was verified with:

```bash
sudo ss -lntup | grep ':53'
```

The Samba DNS process was confirmed to be listening on port `53` for both UDP and TCP traffic.

The final configuration uses Samba's internal DNS service rather than a separate BIND DNS server.

## DNS and Active Directory

The DNS configuration provides the service discovery required by the Samba Active Directory environment:

```text
SAMBALAB.LOCAL
│
└── DC01
    │
    ├── DNS
    │   └── Port 53
    │
    ├── Kerberos
    │   └── Port 88
    │
    └── LDAP
        └── Port 389
```

Active Directory service records allow clients to automatically locate the appropriate services on `DC01`.

## Verification Status

| Test                           | Result      |
| ------------------------------ | ----------- |
| `dc01.sambalab.local` A record | Successful  |
| Kerberos SRV record            | Successful  |
| LDAP SRV record                | Successful  |
| Samba DNS listener             | Running     |
| DNS Port 53                    | Available   |
| Active Directory DNS           | Operational |

The SambaLAB DNS infrastructure is operational and correctly providing the DNS records required by the Active Directory domain.
