# SambaLAB — Group Policy Management

## Overview

Group Policy Objects (GPOs) provide centralized management of security and configuration settings within the Samba Active Directory domain.

SambaLAB includes the default Active Directory policies created during domain provisioning and a custom GPO named **SambaLAB Workstation Security Policy**.

## Existing Group Policies

The domain initially contained two default GPOs:

| GPO                                 | Purpose                                               |
| ----------------------------------- | ----------------------------------------------------- |
| `Default Domain Policy`             | Provides default policies for the SambaLAB domain     |
| `Default Domain Controllers Policy` | Provides policies specifically for Domain Controllers |

These policies were verified using:

```bash
sudo samba-tool gpo listall
```

## Custom GPO

A custom GPO was created to provide a dedicated location for workstation security policies:

```text
SambaLAB Workstation Security Policy
```

The GPO was created using:

```bash
sudo samba-tool gpo create "SambaLAB Workstation Security Policy" \
  -U 'Administrator@SAMBALAB.LOCAL'
```

The resulting GPO identifier is:

```text
{08067C52-8C0B-41F1-A327-6114CA43FD38}
```

The GPO is stored in the domain SYSVOL structure:

```text
\\sambalab.local\sysvol\sambalab.local\Policies\
{08067C52-8C0B-41F1-A327-6114CA43FD38}
```

## GPO Linking

The custom GPO was linked to the departmental OUs so that workstation security policies can be centrally applied to users and computers within each department.

The departmental structure is:

```text
DC=sambalab,DC=local
│
├── OU=IT
│   └── SambaLAB Workstation Security Policy
│
├── OU=HR
│   └── SambaLAB Workstation Security Policy
│
├── OU=Finance
│   └── SambaLAB Workstation Security Policy
│
└── OU=Sales
    └── SambaLAB Workstation Security Policy
```

For example, the GPO link for the IT OU was verified with:

```bash
sudo samba-tool gpo getlink "OU=IT,DC=sambalab,DC=local" \
  -U 'Administrator@SAMBALAB.LOCAL'
```

The verification returned:

```text
GPO(s) linked to DN OU=IT,DC=sambalab,DC=local
    GPO     : {08067C52-8C0B-41F1-A327-6114CA43FD38}
    Name    : SambaLAB Workstation Security Policy
    Options : NONE
```

## Verification

The GPO links were verified for the departmental OUs to ensure that the custom policy is associated with the intended organizational structure.

The GPO is currently configured as a dedicated policy container. Specific workstation security settings will be configured in the subsequent **GPO Configuration** stage.

## Result

The SambaLAB Active Directory environment now has a custom Group Policy Object that can be centrally managed and linked to departmental OUs.

This provides the foundation for implementing workstation security controls such as password policies, account restrictions, desktop configuration, and other centralized security settings.
