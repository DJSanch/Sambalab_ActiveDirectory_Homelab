# SambaLAB — Samba Installation & Active Directory Domain Controller

## Overview

Samba was installed on `DC01` as the foundation for the Samba Active Directory Domain Controller.

The system is running Ubuntu 26.04 LTS on an ARM64 virtual machine hosted through UTM/QEMU.

The Samba installation was subsequently configured and provisioned as a fully functional Active Directory Domain Controller using Samba's internal DNS server and Kerberos authentication.

---

## Samba Version

Samba was installed from the Ubuntu package repositories.

```bash
samba --version
samba-tool --version
```

Installed version:

```text
Samba 4.23.6-Ubuntu-4.23.6+dfsg-1ubuntu2.2
```

Both `samba` and `samba-tool` report the same version.

---

## Required Packages

The primary packages installed for the Samba Active Directory environment were:

```bash
sudo apt install samba-ad-dc krb5-user bind9-dnsutils
```

LDAP utilities were subsequently installed for LDAP testing:

```bash
sudo apt install ldap-utils
```

The packages provide the following functionality:

| Package          | Purpose                                      |
| ---------------- | -------------------------------------------- |
| `samba-ad-dc`    | Samba Active Directory Domain Controller     |
| `krb5-user`      | Kerberos client utilities                    |
| `bind9-dnsutils` | DNS troubleshooting utilities such as `host` |
| `ldap-utils`     | LDAP testing utilities such as `ldapsearch`  |

---

## Kerberos Package Configuration

During installation, the Kerberos configuration package requested information about the SambaLAB Kerberos realm.

The default realm was configured as:

```text
SAMBALAB.LOCAL
```

No external Kerberos server or administrative server was specified during package installation because the Active Directory domain was provisioned using Samba.

Samba provides the required Kerberos Key Distribution Center (KDC) as part of the Active Directory Domain Controller.

The generated Samba Kerberos configuration is located at:

```text
/var/lib/samba/private/krb5.conf
```

The configuration contains:

```ini
[libdefaults]
    default_realm = SAMBALAB.LOCAL
    dns_lookup_realm = false
    dns_lookup_kdc = true
```

---

## Hostname Configuration

The Domain Controller hostname was configured as:

```text
dc01.sambalab.local
```

The short hostname is:

```text
dc01
```

The Domain Controller uses the following static IPv4 address:

```text
192.168.0.200
```

The local `/etc/hosts` configuration maps the Domain Controller to its static IPv4 address:

```text
192.168.0.200   dc01.sambalab.local dc01
```

Hostname resolution was verified with:

```bash
getent hosts dc01
```

Result:

```text
192.168.0.200   dc01.sambalab.local dc01
```

FQDN resolution was also verified:

```bash
getent hosts dc01.sambalab.local
```

Result:

```text
192.168.0.200   dc01.sambalab.local dc01
```

---

## Active Directory Configuration

The Samba server was successfully provisioned as an Active Directory Domain Controller.

The resulting configuration is:

| Setting           | Value                              |
| ----------------- | ---------------------------------- |
| Domain            | `sambalab.local`                   |
| Kerberos Realm    | `SAMBALAB.LOCAL`                   |
| NetBIOS Domain    | `SAMBALAB`                         |
| Domain Controller | `DC01`                             |
| FQDN              | `dc01.sambalab.local`              |
| IPv4              | `192.168.0.200`                    |
| Server Role       | Active Directory Domain Controller |
| DNS Backend       | Samba Internal DNS                 |
| Kerberos KDC      | Samba                              |
| LDAP Directory    | Samba AD LDAP                      |

The Samba configuration was validated using:

```bash
sudo testparm -s
```

The configuration reported:

```text
Server role: ROLE_ACTIVE_DIRECTORY_DC
```

---

## Samba AD DC Service

The Samba Active Directory service is managed using:

```bash
sudo systemctl status samba-ad-dc --no-pager
```

The service was successfully started and reported:

```text
Active: active (running)
Status: "samba: ready to serve connections..."
```

The service contains the major AD components required by the domain controller, including:

* SMB
* LDAP
* Kerberos
* Winbind
* DNS
* RPC
* NetBIOS
* DNS update services

The standalone `smbd`, `nmbd`, and `winbind` services are not required to be independently enabled when Samba is operating as an AD Domain Controller. Their required functionality is managed by the `samba-ad-dc` service.

---

## Samba Internal DNS

SambaLAB uses Samba's internal DNS server.

The Samba DNS service listens on port `53`.

The DNS listener was verified using:

```bash
sudo ss -lntup | grep ':53'
```

The expected Samba DNS listeners were observed:

```text
udp   UNCONN 0 0 0.0.0.0:53
tcp   LISTEN 0 10 0.0.0.0:53
```

This confirms that Samba is listening for DNS requests on the server's IPv4 interfaces.

### DNS Configuration Issue During Setup

During the initial startup, Samba's DNS service could not bind to IPv4 port `53` because another resolver was already occupying the required address.

The Samba logs initially reported:

```text
Failed to listen on 0.0.0.0:53
NT_STATUS_ADDRESS_ALREADY_ASSOCIATED
```

The system was also using `systemd-resolved` with:

```text
nameserver 127.0.0.53
```

`systemd-resolved` was subsequently stopped so that Samba could provide the authoritative DNS service required by the Active Directory environment.

After restarting Samba, the DNS service successfully bound to:

```text
0.0.0.0:53
```

DNS functionality was then verified successfully.

---

## DNS Verification

The Domain Controller's A record was tested with:

```bash
host -t A dc01.sambalab.local
```

Result:

```text
dc01.sambalab.local has address 192.168.0.200
```

Kerberos service discovery was tested with:

```bash
host -t SRV _kerberos._udp.sambalab.local
```

Result:

```text
_kerberos._udp.sambalab.local has SRV record 0 100 88 dc01.sambalab.local.
```

LDAP service discovery was tested with:

```bash
host -t SRV _ldap._tcp.sambalab.local
```

Result:

```text
_ldap._tcp.sambalab.local has SRV record 0 100 389 dc01.sambalab.local.
```

These records confirm that clients can discover the Domain Controller's Kerberos and LDAP services through Active Directory DNS.

---

## Kerberos Verification

Kerberos authentication was successfully tested using the domain Administrator account.

First, any existing ticket was removed:

```bash
kdestroy
```

A new Kerberos ticket was then requested:

```bash
kinit Administrator@SAMBALAB.LOCAL
```

The resulting ticket was verified with:

```bash
klist
```

Result:

```text
Credentials cache: FILE:/tmp/krb5cc_1000
Principal: Administrator@SAMBALAB.LOCAL

Issued                Expires               Principal
Aug 28 11:28:39 2026  Aug 28 21:28:39 2026  krbtgt/SAMBALAB.LOCAL@SAMBALAB.LOCAL
```

This confirms that:

* The Kerberos realm is operational.
* The Samba KDC is reachable.
* The `Administrator` account can authenticate.
* A Ticket Granting Ticket (TGT) was successfully issued.

---

## Domain Information

The Active Directory domain was verified using:

```bash
sudo samba-tool domain info dc01.sambalab.local
```

Result:

```text
Forest           : sambalab.local
Domain           : sambalab.local
Netbios domain   : SAMBALAB
DC name          : dc01.sambalab.local
DC netbios name  : DC01
Server site      : Default-First-Site-Name
Client site      : Default-First-Site-Name
```

This confirms that `DC01` is recognized as the Domain Controller for the `SAMBALAB` domain.

---

## Domain and Forest Functional Levels

The Active Directory functional levels were checked using:

```bash
sudo samba-tool domain level show
```

Current configuration:

```text
Forest function level: (Windows) 2008 R2
Domain function level: (Windows) 2008 R2
Lowest function level of a DC: (Windows) 2008 R2
```

The domain and forest therefore currently operate at the Windows Server 2008 R2 functional level.

---

## LDAP Verification

LDAP functionality was tested using Kerberos authentication through SASL/GSSAPI.

The following command was used:

```bash
ldapsearch -H ldap://dc01.sambalab.local \
  -Y GSSAPI \
  -b "DC=sambalab,DC=local" \
  "(objectClass=user)" sAMAccountName
```

The authentication succeeded:

```text
SASL/GSSAPI authentication started
SASL username: Administrator@SAMBALAB.LOCAL
SASL SSF: 256
SASL data security layer installed.
```

The LDAP directory returned the following domain accounts:

```text
krbtgt
Administrator
DC01$
Guest
```

The search completed successfully:

```text
result: 0 Success
```

This confirms that the Samba Active Directory LDAP directory is operational and can be accessed using Kerberos authentication.

---

## Initial Active Directory Objects

The LDAP directory currently contains the standard objects created during domain provisioning.

Examples include:

| Object          | Location                |
| --------------- | ----------------------- |
| `Administrator` | `CN=Users`              |
| `Guest`         | `CN=Users`              |
| `krbtgt`        | `CN=Users`              |
| `DC01$`         | `OU=Domain Controllers` |

The `DC01$` computer account represents the Domain Controller within Active Directory.

---

## Active Directory DNS Structure

The Samba Active Directory environment contains the DNS infrastructure required by AD, including:

```text
sambalab.local
├── _kerberos
├── _ldap
├── _msdcs
├── DomainDnsZones
└── ForestDnsZones
```

These DNS records are required for Active Directory service discovery and authentication.

---

## Current Installation Status

| Component               | Status                |
| ----------------------- | --------------------- |
| Ubuntu 26.04 LTS        | Configured            |
| Static IPv4             | `192.168.0.200`       |
| Hostname                | Configured            |
| FQDN                    | `dc01.sambalab.local` |
| `/etc/hosts`            | Configured            |
| Samba                   | Installed             |
| `samba-tool`            | Installed             |
| Kerberos client         | Installed             |
| DNS utilities           | Installed             |
| LDAP utilities          | Installed             |
| Active Directory Domain | Provisioned           |
| Samba AD DC             | Running               |
| Internal DNS            | Operational           |
| Kerberos KDC            | Operational           |
| LDAP Directory          | Operational           |
| Domain Discovery        | Verified              |
| Domain Functional Level | Windows 2008 R2       |
| Forest Functional Level | Windows 2008 R2       |

---

## Installation Status

The SambaLAB Domain Controller is now operational.

The following core Active Directory services have been successfully verified:

```text
                    SAMBALAB.LOCAL
                           │
                           ▼
                  ┌─────────────────┐
                  │       DC01      │
                  │ 192.168.0.200   │
                  └────────┬────────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
        DNS             Kerberos          LDAP
        :53               :88             :389
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                  Active Directory
```

The environment is ready for the next stage of the SambaLAB project:

1. Create Organizational Units (OUs)
2. Create security groups
3. Create domain users
4. Configure user/group permissions
5. Create computer accounts
6. Configure Group Policy
7. Join a client machine to `SAMBALAB.LOCAL`
8. Test domain authentication
9. Test DNS and Kerberos from a domain client
10. Configure file shares and Active Directory permissions
11. Document administrative procedures and troubleshooting
