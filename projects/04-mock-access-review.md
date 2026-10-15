# Project 4: Mock User Access Review

**Date:** October 7, 2026

## Objective
Run a periodic access review on `billsec.local`: compare each user's group memberships with their department and role, find access that is not justified, remove it, and prove the result.

## Environment
Windows Server 2022 (WS-DC01), PowerShell with the Active Directory module, run in an elevated session.

## Method
1. Listed members of every role group:

```powershell
Get-ADGroup -Filter * -SearchBase "OU=Groups,DC=billsec,DC=local" | ForEach-Object { $g = $_.Name; Get-ADGroupMember -Identity $g | Select-Object @{n='Group';e={$g}}, Name, SamAccountName }
```

2. Pulled each user's department from their OU placement:

```powershell
Get-ADUser -Filter * -SearchBase "OU=Employees,DC=billsec,DC=local" | Select-Object Name, SamAccountName, DistinguishedName | Format-List
```

3. Compared department, groups, and expected access for each user.

## Review results

| User | Department | Groups | Decision |
|---|---|---|---|
| Billy Mitchell | IT | Manager, All-Employees | Keep |
| Sarah Tech | IT | Helpdesk, All-Employees | Keep |
| Mike Numbers | Finance | Intern, All-Employees | Keep |
| Jamie Sales | Sales | Helpdesk, All-Employees | **Revoke Helpdesk** |

## Finding
Jamie Sales works in Sales but was a member of Helpdesk, an IT support role. Nothing in her department justified that access. It was left over from an earlier group-membership fix, which is how excess access builds up over time (privilege creep).

## Remediation
```powershell
Remove-ADGroupMember -Identity "Helpdesk" -Members jsales -Confirm:$false
Get-ADGroupMember -Identity "Helpdesk" | Select-Object Name, SamAccountName
```

The first attempt failed with "Insufficient access rights" because PowerShell was not elevated, the same UAC issue as in Project 2. After relaunching as Administrator, the removal worked, and re-running the group query showed only Sarah Tech.

## IAM principles
- An access review checks that existing access is still justified, not only that new access was granted correctly.
- Comparing group membership to job function supports least privilege.
- A review is complete only after remediation and verification, not when the findings list is written.
- In a real organization, a manager would approve or reject each person's access, and the decisions would be kept for auditors. This lab review shows the technical side of that process.
