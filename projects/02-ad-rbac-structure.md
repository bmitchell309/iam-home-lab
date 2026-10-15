# Project 2: Building an RBAC Structure in Active Directory

**Date:** September 6, 2026

## Objective
Design a small-organization Active Directory structure that combines department-based Organizational Units (OUs) with role-based security groups, and verify every change with PowerShell instead of trusting the GUI.

## Environment
Windows Server 2022 (WS-DC01), Active Directory Users and Computers (ADUC), PowerShell with the Active Directory module.

## What I built
- Department sub-OUs (`IT`, `Finance`, `Sales`) inside an `Employees` OU.
- Four security groups in a `Groups` OU: `Helpdesk`, `Manager`, `Intern`, and `All-Employees`.
- Test users placed in their department OU, each assigned a role-based group plus `All-Employees`. This shows that one person can belong to several groups that reflect different things: department placement versus permission level.

## Problem
Verifying with `Get-ADUser -Properties MemberOf` showed some users with no group memberships, even though the GUI had appeared to save them. Three groups saved correctly; `Helpdesk` did not.

## Troubleshooting
1. Confirmed usernames with `Get-ADUser -Filter * -SearchBase "OU=Employees,DC=billsec,DC=local"`, ruling out a typo.
2. Checked from the group's side with `Get-ADGroupMember`, which isolated the failure to `Helpdesk` only.
3. Tried `Add-ADGroupMember`, which failed with "Insufficient access rights," even though `badmin` is a Domain Admin.
4. Found the cause: PowerShell was not running as Administrator. Under User Account Control (UAC) token filtering, an administrator's elevated rights are not applied to a non-elevated session.

## Resolution
Relaunched PowerShell with "Run as administrator," re-ran `Add-ADGroupMember`, and confirmed all four groups with `Get-ADGroupMember`.

## IAM principles
- Group membership is how access is controlled, and it has to be verified programmatically, not assumed from GUI feedback.
- Holding a privilege and exercising it in a session are different things. A Domain Admin can still be blocked if the session is not elevated.
