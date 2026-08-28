# SambaLAB — Lab Design

## 1. Domain Design

SambaLAB uses a single Active Directory domain hosted by a Samba Active Directory Domain Controller.

| Setting                   | Value                 |
| ------------------------- | --------------------- |
| Domain                    | `sambalab.local`      |
| Kerberos Realm            | `SAMBALAB.LOCAL`      |
| NetBIOS Domain            | `SAMBALAB`            |
| Primary Domain Controller | `DC01`                |
| Domain Controller FQDN    | `dc01.sambalab.local` |
| IP Address                | `192.168.0.200`       |

The environment currently operates with a single Domain Controller. Additional Domain Controllers may be added in the future for redundancy and replication testing.

---

## 2. Organizational Unit Design

Organizational Units are used to separate departmental user accounts and provide a foundation for applying policies and administrative controls.

Current departmental OUs:

```text
sambalab.local
│
├── IT
├── HR
├── Finance
└── Sales
```

The Samba default `Domain Controllers` OU is also present:

```text
sambalab.local
│
└── Domain Controllers
    └── DC01
```

The departmental OUs contain the corresponding user accounts.

Each department currently contains 20 users:

| Organizational Unit |  Users |
| ------------------- | -----: |
| IT                  |     20 |
| HR                  |     20 |
| Finance             |     20 |
| Sales               |     20 |
| **Total**           | **80** |

Additional OUs for computers, servers, and administrative accounts may be introduced as the lab expands.

---

## 3. Security Group Design

Department membership is managed through security groups rather than assigning permissions directly to individual users wherever possible.

### Department Groups

```text
IT-Users
HR-Users
Finance-Users
Sales-Users
```

### Administrative Groups

The domain also provides built-in and administrative security groups that can be used for role-based administration.

Examples include:

```text
Domain Admins
Server Operators
Account Operators
Backup Operators
DnsAdmins
```

Additional custom administrative groups may be introduced as administrative delegation scenarios are implemented.

The group-based access model follows the principle:

```text
User
 │
 ▼
Security Group
 │
 ▼
Resource
```

---

## 4. Naming Convention

### Domain Controller

```text
DC01
```

Additional Domain Controllers will follow the same naming pattern:

```text
DC02
DC03
```

### File Servers

Future file servers will use:

```text
FILE01
FILE02
```

### Windows Clients

Windows domain clients will use a department-based naming convention:

```text
IT-PC-001
HR-PC-001
FIN-PC-001
SALES-PC-001
```

### User Accounts

Usernames follow a short, standardized naming convention.

Format:

```text
[first initial][lastname]
```

Examples:

```text
dsanchez
jmartinez
agarcia
```

### Service Accounts

Service accounts will use:

```text
svc-<service>
```

Examples:

```text
svc-backup
svc-monitoring
```

---

## 5. Network Design

SambaLAB operates on a private network used by the UTM virtualized environment.

The Domain Controller uses a static IPv4 address.

| Device            | Hostname    | IP Address      | Role            |
| ----------------- | ----------- | --------------- | --------------- |
| Domain Controller | `DC01`      | `192.168.0.200` | Samba AD + DNS  |
| File Server       | `FILE01`    | Future          | SMB/File Server |
| Windows Client    | `IT-PC-001` | Future          | Domain Client   |

The Domain Controller provides internal DNS services for the `sambalab.local` domain.

DNS records have been verified for:

```text
dc01.sambalab.local
_kerberos._udp.sambalab.local
_ldap._tcp.sambalab.local
```

---

## 6. Server Roles

### DC01

The primary Domain Controller provides:

* Samba Active Directory Domain Controller
* Internal DNS
* Kerberos authentication
* LDAP directory services
* Domain authentication
* Active Directory database
* Group Policy infrastructure

### FILE01

A dedicated file server is planned for a future phase.

Planned services include:

* SMB/CIFS
* Department file shares
* Centralized file storage
* Group-based access control
* Department-specific permissions

---

## 7. Access Control Model

SambaLAB follows a role- and group-based access control model.

```text
User
 │
 ▼
Security Group
 │
 ▼
Resource
```

For example:

```text
Finance User
      │
      ▼
Finance-Users
      │
      ▼
\\FILE01\Finance
```

Departmental resources will be restricted using security groups rather than individual user permissions whenever possible.

Users should not automatically receive access to restricted resources belonging to other departments.

---

## 8. Administrative Model

Administrative responsibilities are intended to be separated according to role.

```text
IT Administrators
├── General IT administration
│
Helpdesk
├── User support
└── Account management
│
Server Administrators
└── Server administration
│
Network Administrators
└── Network administration
```

Privileged operations should be performed using dedicated administrative accounts rather than standard user accounts.

The lab will use least-privilege principles wherever practical.

---

## 9. Current Implementation Status

The core Active Directory environment has been successfully implemented.

| Component               | Status      |
| ----------------------- | ----------- |
| Samba AD DC             | Implemented |
| Active Directory Domain | Implemented |
| DNS                     | Implemented |
| Kerberos                | Implemented |
| LDAP                    | Implemented |
| DC01                    | Implemented |
| IT OU                   | Implemented |
| HR OU                   | Implemented |
| Finance OU              | Implemented |
| Sales OU                | Implemented |
| Department Users        | 80 users    |
| Security Groups         | Implemented |
| Group Policy            | Implemented |
| Password Policy         | Implemented |
| AD Verification         | Completed   |

---

## 10. Future Expansion

The following components are planned for future development:

* Additional Domain Controller (`DC02`)
* Windows domain clients
* Windows domain joining
* File server (`FILE01`)
* SMB departmental shares
* Department-based file permissions
* Administrative delegation
* Helpdesk administration scenarios
* DHCP
* Infrastructure monitoring
* Backup server
* Disaster recovery testing
* Additional Group Policy configurations
* Active Directory replication testing

The lab design will be updated as additional infrastructure and administration scenarios are implemented.

---

## 11. Design Principles

SambaLAB is designed around several fundamental IT administration principles:

* Centralized identity management
* Role-based access control
* Least-privilege administration
* Separation of administrative responsibilities
* Standardized naming conventions
* Centralized authentication
* Secure resource access
* Infrastructure documentation
* Repeatable configuration and verification procedures

The design is intended to provide a foundation for progressively introducing more realistic enterprise administration scenarios.
