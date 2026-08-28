# SambaLAB — Users and Groups Management

## Overview

Departmental Organizational Units (OUs), security groups, and user accounts were created in the Samba Active Directory domain. The environment is organized into four departments: **IT, HR, Finance, and Sales**.

Each department contains **20 user accounts**, providing a realistic Active Directory structure for the SambaLAB homelab.

## Department Structure

| Department | OU           | Security Group  | Users |
| ---------- | ------------ | --------------- | ----: |
| IT         | `OU=IT`      | `IT-Users`      |    20 |
| HR         | `OU=HR`      | `HR-Users`      |    20 |
| Finance    | `OU=Finance` | `Finance-Users` |    20 |
| Sales      | `OU=Sales`   | `Sales-Users`   |    20 |

The resulting directory structure is:

```text
DC=sambalab,DC=local
│
├── OU=IT
│   └── IT01 – IT20
│
├── OU=HR
│   └── HR01 – HR20
│
├── OU=Finance
│   └── Finance01 – Finance20
│
└── OU=Sales
    └── Sales01 – Sales20
```

## User Creation

Users were created using `samba-tool user create` and placed directly into their respective departmental OUs.

Example:

```bash
sudo samba-tool user create it01 \
  --userou="OU=IT,DC=sambalab,DC=local"
```

The resulting account was verified with:

```bash
sudo samba-tool user show it01
```

The account was successfully created with the distinguished name:

```text
CN=it01,OU=IT,DC=sambalab,DC=local
```

## Group Management

A security group was created for each department:

```bash
sudo samba-tool group add IT-Users
sudo samba-tool group add HR-Users
sudo samba-tool group add Finance-Users
sudo samba-tool group add Sales-Users
```

Users were then assigned to their corresponding departmental groups.

For example:

```bash
sudo samba-tool group addmembers IT-Users it01
```

Group membership can be verified with:

```bash
sudo samba-tool group listmembers IT-Users
```

## Verification

The completed environment contains:

* **20 IT users**
* **20 HR users**
* **20 Finance users**
* **20 Sales users**
* **80 departmental user accounts total**

A compact verification was performed to confirm the number of users in each departmental group and verify that sample accounts are located in the correct OUs.

## Result

The SambaLAB Active Directory environment now contains a structured departmental user organization with dedicated OUs and security groups. This structure provides the foundation for future **Group Policy, permissions, file-share access control, and Windows client domain-join testing**.
