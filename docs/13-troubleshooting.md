# SambaLAB — Troubleshooting and Final Verification

## Overview

During the implementation of the SambaLAB Active Directory Domain Controller, several configuration and connectivity issues were identified and resolved. Troubleshooting focused primarily on DNS, Kerberos authentication, Samba service operation, and LDAP connectivity.

After resolving these issues, the Active Directory environment was subjected to a series of final verification tests.

## Troubleshooting

### DNS Port Conflict

The Samba AD DNS service initially failed to bind to IPv4 port 53 because `systemd-resolved` was already using the DNS stub listener.

The Samba logs reported:

```text
Failed to listen on 0.0.0.0:53
NT_STATUS_ADDRESS_ALREADY_ASSOCIATED
```

The issue was identified by checking which services were listening on port 53:

```bash
sudo ss -lntup | grep ':53'
```

`systemd-resolved` was subsequently stopped, allowing Samba's internal DNS server to bind to port 53.

After restarting Samba, the DNS service successfully listened on IPv4:

```text
0.0.0.0:53
```

### DNS Resolution Failure

The initial DNS configuration caused `samba_dnsupdate` to report failures when attempting to contact the local DNS server.

The problem was related to the system resolver configuration and the conflict with `systemd-resolved`.

After correcting the DNS configuration and restarting Samba, DNS records could be queried successfully.

### Kerberos Authentication

Kerberos initially failed with:

```text
kinit: krb5_get_init_creds: unable to reach any KDC in realm SAMBALAB.LOCAL
```

This was investigated by checking the Kerberos configuration and DNS SRV records.

Once DNS was functioning correctly, Kerberos authentication succeeded:

```bash
kinit Administrator@SAMBALAB.LOCAL
```

The resulting ticket was verified with:

```bash
klist
```

The Kerberos ticket-granting ticket was successfully issued for:

```text
Administrator@SAMBALAB.LOCAL
```

### LDAP Authentication

LDAP authentication was tested using Kerberos/GSSAPI:

```bash
ldapsearch -H ldap://dc01.sambalab.local \
  -Y GSSAPI \
  -b "DC=sambalab,DC=local" \
  "(objectClass=user)" sAMAccountName
```

The query successfully returned the domain user objects, including:

```text
krbtgt
Administrator
DC01$
Guest
```

This confirmed that LDAP directory services were operational and that Kerberos authentication could be used to access the directory.

### GPO Authentication Issue

An initial GPO command returned:

```text
LDAP error 49 LDAP_INVALID_CREDENTIALS
```

The issue was caused by authentication credentials rather than a GPO configuration failure.

Using the domain Administrator UPN resolved the issue:

```bash
-U 'Administrator@SAMBALAB.LOCAL'
```

The GPO link was subsequently verified successfully.

## Final Verification

The Samba AD Domain Controller was verified using the following tests.

### Samba Service

```bash
sudo systemctl status samba-ad-dc --no-pager
```

Result:

```text
Active: active (running)
Status: "samba: ready to serve connections..."
```

### Domain Information

```bash
sudo samba-tool domain info dc01.sambalab.local
```

Verified configuration:

| Setting           | Value                     |
| ----------------- | ------------------------- |
| Forest            | `sambalab.local`          |
| Domain            | `sambalab.local`          |
| NetBIOS Domain    | `SAMBALAB`                |
| Domain Controller | `dc01.sambalab.local`     |
| DC NetBIOS Name   | `DC01`                    |
| Site              | `Default-First-Site-Name` |

### DNS Verification

The Domain Controller A record was verified:

```bash
host -t A dc01.sambalab.local
```

Result:

```text
dc01.sambalab.local has address 192.168.0.200
```

Kerberos service discovery was verified:

```bash
host -t SRV _kerberos._udp.sambalab.local
```

LDAP service discovery was verified:

```bash
host -t SRV _ldap._tcp.sambalab.local
```

The results confirmed that both Kerberos and LDAP services resolve to `dc01.sambalab.local`.

### Kerberos Verification

```bash
kinit Administrator@SAMBALAB.LOCAL
klist
```

A valid Kerberos ticket-granting ticket was issued for:

```text
Administrator@SAMBALAB.LOCAL
```

### LDAP Verification

LDAP directory access was successfully tested using GSSAPI authentication.

The query returned the expected Active Directory objects and completed with:

```text
result: 0 Success
```

### GPO Verification

The custom GPO was successfully created:

```text
SambaLAB Workstation Security Policy
```

GPO GUID:

```text
{08067C52-8C0B-41F1-A327-6114CA43FD38}
```

The GPO link was verified against the IT OU:

```text
GPO(s) linked to DN OU=IT,DC=sambalab,DC=local
    GPO     : {08067C52-8C0B-41F1-A327-6114CA43FD38}
    Name    : SambaLAB Workstation Security Policy
    Options : NONE
```

## Final Environment Status

| Component                 | Status           |
| ------------------------- | ---------------- |
| Ubuntu Server             | Operational      |
| Samba AD DC               | Operational      |
| Domain                    | `sambalab.local` |
| NetBIOS Domain            | `SAMBALAB`       |
| DC01                      | Operational      |
| Internal DNS              | Operational      |
| Kerberos                  | Operational      |
| LDAP                      | Operational      |
| Active Directory database | Operational      |
| Departmental OUs          | Configured       |
| User accounts             | Configured       |
| Security groups           | Configured       |
| Custom GPO                | Created          |
| GPO links                 | Configured       |
| Password policy           | Configured       |

## Conclusion

The SambaLAB environment successfully operates as a Samba Active Directory Domain Controller. DNS, Kerberos, LDAP, user management, Group Policy, and domain services were individually tested after configuration and troubleshooting.

The completed environment provides a functional Active Directory laboratory suitable for practicing enterprise identity management, authentication, DNS administration, Group Policy management, and Windows/Linux domain integration.
