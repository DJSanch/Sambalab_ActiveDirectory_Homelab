# SambaLAB — Lab Design

## 1. Domain Design

| Setting                   | Value                 |
| ------------------------- | --------------------- |
| Domain                    | `sambalab.local`      |
| Realm                     | `SAMBALAB.LOCAL`      |
| NetBIOS Domain            | `SAMBALAB`            |
| Primary Domain Controller | `DC01`                |
| Domain Controller FQDN    | `dc01.sambalab.local` |

---

## 2. Organizational Unit Design

The Active Directory structure will separate users, computers, servers, and administrative accounts.

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

OUs will be used to organize directory objects and provide a foundation for applying administrative policies.

---

## 3. Security Group Design

Department membership will be managed through security groups.

### Department Groups

```text
IT-Staff
HR-Staff
Finance-Staff
Sales-Staff
```

### Administrative Groups

```text
IT-Admins
Helpdesk
Server-Admins
Network-Admins
```

Access to resources will be assigned to groups rather than individual users wherever possible.

---

## 4. Naming Convention

### Domain Controllers

```text
DC01
DC02
```

### File Servers

```text
FILE01
FILE02
```

### Windows Clients

```text
IT-PC-001
HR-PC-001
FIN-PC-001
SALES-PC-001
```

### User Accounts

Usernames will use the following format:

```text
first initial + lastname
```

Example:

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

Example:

```text
svc-backup
svc-monitoring
```

---

## 5. Network Design

SambaLAB will use a private virtual network hosted through UTM.

The Active Directory Domain Controller will use a static IP address.

The final IP addressing plan will be documented after the network is configured.

| Device            | Hostname    | IP Address | Role            |
| ----------------- | ----------- | ---------- | --------------- |
| Domain Controller | `DC01`      | TBD        | Samba AD + DNS  |
| File Server       | `FILE01`    | TBD        | SMB/File Server |
| Windows Client    | `IT-PC-001` | DHCP/TBD   | Domain Client   |

---

## 6. Server Roles

### DC01

Primary services:

* Samba Active Directory Domain Controller
* DNS
* Kerberos
* LDAP directory services
* Domain authentication

### FILE01

Planned services:

* SMB/CIFS
* Department file shares
* Centralized file storage
* Group-based access control

---

## 7. Access Control Model

SambaLAB will use **role- and group-based access control**.

```text
User
 │
 ▼
Security Group
 │
 ▼
Resource
```

Example:

```text
Finance User
      │
      ▼
Finance-Staff
      │
      ▼
\\FILE01\Finance
```

Users from other departments should not automatically have access to restricted departmental resources.

---

## 8. Administrative Model

Administrative privileges will be separated according to responsibility.

```text
IT-Admins
├── General IT administration

Helpdesk
├── User support
└── Account management

Server-Admins
└── Server administration

Network-Admins
└── Network administration
```

A separate administrative account will be used for privileged operations instead of using a normal user account for administration.

---

## 9. Future Expansion

The initial environment will begin with a single Domain Controller.

Potential future additions include:

* Additional Domain Controller
* Windows domain clients
* File server
* DHCP
* Monitoring
* Backup server
* Additional security groups
* Administrative delegation
* Group Policy testing
* Disaster recovery testing

The design will be updated as these components are implemented.
