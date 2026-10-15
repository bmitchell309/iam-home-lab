# Project 3: Linux Identity, Sudo Scoping, and SSH Keys

**Date:** September 27, 2026

## Objective
Practice Linux identity and access management on `ubuntu-lab`: local users and groups, role-based sudo privileges, SSH key-based authentication, and authentication log review.

## Environment
Ubuntu Server 24.04 LTS, reached over SSH from the Windows host through a VirtualBox port-forwarding rule.

## What I built
- Three local groups: `sysadmins`, `helpdesk`, `interns`.
- Three test users, one per group: `jadmin`, `hdesk`, `intern1`. Passwords are kept in a password manager, not in this repository.
- Sudo privileges set through separate files in `/etc/sudoers.d/`, edited with `visudo -f` so syntax is checked before saving:
  - `sysadmins`: full sudo access.
  - `helpdesk`: only service status and restart commands and reboot. No general root access.
  - `interns`: no sudo rule at all.
- An ed25519 SSH key pair on the host, with the public key in the server's `authorized_keys` and permissions set to 700 on `~/.ssh` and 600 on the file.

## Verification
I tested each access level by logging in as each user rather than trusting the configuration files:

| User | Test | Result |
|---|---|---|
| `jadmin` | `sudo whoami` | Returned `root` |
| `hdesk` | `sudo systemctl status ssh` | Allowed |
| `hdesk` | `sudo whoami` | Denied: user is not allowed to run it |
| `intern1` | `sudo whoami` | Denied: user is not in the sudoers file |

The two denials have different messages. The helpdesk message shows a rule exists but does not cover that command, and the intern message shows no rule exists at all.

For SSH, I confirmed that key login worked without a password prompt, then checked `/var/log/auth.log`. A key login records `Accepted publickey`, and a password login records `Accepted password`.

## Decision on password login
Password authentication is still enabled as a fallback so I cannot lock myself out while I confirm the key setup is reliable. Key-only SSH is on the roadmap once the key is backed up and tested across sessions.

## IAM principles
- Group membership alone grants nothing. Access has to be configured separately, which is why testing the boundaries matters.
- Scope each role to the minimum it needs (least privilege).
- Authentication method shows up in logs, which matters for monitoring and audits.
