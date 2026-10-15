# Project 1: Diagnosing a Domain Logon Failure

**Date:** September 5, 2026

## Objective
Create a separate administrative account (`badmin`) instead of relying only on the built-in Administrator account, following least privilege and separation of duties.

## Environment
Windows Server 2022 (WS-DC01), Active Directory Domain Services, domain `billsec.local`.

## Problem
After creating the domain user `badmin`, an interactive logon on the domain controller was denied with:

- "The sign-in method you're trying to use isn't allowed."
- "The user has not been granted the requested logon type at this machine."

## Troubleshooting
1. Confirmed `badmin` was an enabled domain account:
   `Get-ADUser -Identity badmin -Properties Enabled, MemberOf`
2. Found `MemberOf` was empty: the account had no group memberships beyond the default.
3. Concluded that logon rights on a domain controller come from group membership, not from an account simply being enabled.

## Resolution
Added the account to Domain Admins and verified:

```powershell
Add-ADGroupMember -Identity "Domain Admins" -Members badmin
Get-ADUser -Identity badmin -Properties MemberOf
```

After signing out and back in for a clean logon, `badmin` logged in successfully.

## IAM principle
Logon rights in Windows and Active Directory are granted through group membership (role-based access control), not by an account flag. Troubleshooting starts by checking what an account actually has with an authoritative tool, rather than assuming from the GUI or from earlier setup steps.
