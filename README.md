# IAM Home Lab

A hands-on Identity and Access Management (IAM) lab built to practice the work of an IAM analyst: access control design, privileged access, access reviews, and audit-ready documentation. Every project here was built, tested, and written up from a real problem, including the failures.

## About

I am a former educator moving into cybersecurity, targeting IAM and security governance roles. I hold CompTIA Security+ and completed the TryHackMe SOC Level 1 pathway. This lab is where I turn that knowledge into practice.

## Lab environment

| Component | Details |
|---|---|
| Host | Windows 11 Pro, Oracle VirtualBox |
| WS-DC01 | Windows Server 2022, Active Directory domain controller for `billsec.local` |
| ubuntu-lab | Ubuntu Server 24.04 LTS |
| Network | VirtualBox NAT Network (VM-to-VM traffic), SSH through a port-forwarding rule |
| Cloud | Free Microsoft Entra ID tenant (Azure) |
| Tools | PowerShell (Active Directory module), ADUC, OpenSSH, sudo, auth.log, Entra admin center |

## Projects

| # | Project | Skills shown |
|---|---|---|
| 1 | [Diagnosing a domain logon failure](projects/01-ad-logon-failure.md) | Active Directory, group-based logon rights, PowerShell troubleshooting |
| 2 | [Building an RBAC structure in Active Directory](projects/02-ad-rbac-structure.md) | OUs, security groups, least privilege, verification, UAC elevation |
| 3 | [Linux identity, sudo scoping, and SSH keys](projects/03-linux-sudo-ssh.md) | Local users and groups, sudoers, key-based authentication, log review |
| 4 | [Mock user access review](projects/04-mock-access-review.md) | Access recertification, privilege creep, remediation, evidence |
| 5 | [Building and reaching the Linux server](projects/05-ubuntu-build-and-ssh-access.md) | Troubleshooting, NAT vs NAT Network, port forwarding |
| 6 | [Access review in Microsoft Entra ID](projects/06-entra-id-access-review.md) | Entra ID users and groups, department attribute, cloud access review, remediation |

## Themes across the lab

- **Verify, do not assume.** Group changes in the GUI did not always save, so every change is confirmed with PowerShell or system logs.
- **Least privilege.** Access is scoped to job function: department OUs for placement, role-based groups for permissions, scoped sudo for Linux.
- **Document everything.** Each project records the objective, problem, resolution, and evidence, the way an audit trail would.

## Roadmap

- Join the Linux server to the `billsec.local` domain
- Add a Windows 11 client machine
- Add an AD-authenticated VPN
- More Microsoft Entra ID exercises (multi-factor authentication, conditional access, built-in access reviews on a trial)
- Security hardening pass (for example, key-only SSH)

## A note on security

This is an isolated lab with test accounts only. Lab credentials are stored in a password manager and are never published in this repository.
