# SambaLAB — GPO Security Configuration

## Overview

Security policies were configured for the SambaLAB Active Directory domain to establish stronger authentication and account protection requirements.

The domain already had password complexity enabled and plaintext password storage disabled. The password policy was strengthened to improve protection against weak passwords and repeated authentication attempts.

## Domain Password Policy

The SambaLAB domain password policy was configured with the following settings:

| Policy                    | Configuration |
| ------------------------- | ------------: |
| Password complexity       |       Enabled |
| Store plaintext passwords |      Disabled |
| Password history          |  24 passwords |
| Minimum password length   | 12 characters |
| Minimum password age      |         1 day |
| Maximum password age      |       90 days |
| Account lockout threshold |    5 attempts |
| Account lockout duration  |    30 minutes |
| Reset lockout counter     |    30 minutes |

The policy was configured using:

```bash
sudo samba-tool domain passwordsettings set \
  --complexity=on \
  --store-plaintext=off \
  --history-length=24 \
  --min-pwd-length=12 \
  --min-pwd-age=1 \
  --max-pwd-age=90 \
  --account-lockout-threshold=5 \
  --account-lockout-duration=30 \
  --reset-account-lockout-after=30
```

## Verification

The resulting domain password policy was verified using:

```bash
sudo samba-tool domain passwordsettings show
```

The command confirms that password complexity is enabled, plaintext password storage is disabled, and the configured password and account-lockout requirements are active.

## GPO Relationship

The custom GPO **SambaLAB Workstation Security Policy** was previously created and linked to the departmental OUs.

The password settings documented in this section are **domain-level Active Directory password policies** configured through `samba-tool`. They are separate from workstation-specific settings that can be configured within the custom GPO.

The custom GPO provides the foundation for future centralized workstation policies such as:

* Screen-lock requirements
* Windows security settings
* User restrictions
* Desktop configuration
* Security auditing
* Workstation-specific controls

## Security Result

The SambaLAB domain now enforces stronger password requirements and account-lockout protection. These controls reduce the risk associated with weak passwords and repeated authentication attempts while providing a foundation for additional centralized workstation security policies.
