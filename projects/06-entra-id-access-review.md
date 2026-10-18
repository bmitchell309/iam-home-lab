# Project 6: Access Review in Microsoft Entra ID

**Date:** October 18, 2026

## Objective
Rebuild a small version of my Active Directory lab in the cloud, then run an access review: compare each user's group membership with their department, find access that is not justified, remove it, and prove the result.

## Environment
A free Microsoft Entra ID tenant, created through a free Azure account and managed in the Entra admin center. This is the cloud counterpart to the on-premises `billsec.local` domain from earlier projects.

## What I built
- Four security groups with assigned membership: `Helpdesk`, `Manager`, `Intern`, and `All-Employees`.
- Three test users with a job title and a department set on each account:

| User | Department | Job title | Groups |
|---|---|---|---|
| Sarah Tech | IT | Help Desk Technician | Helpdesk, All-Employees |
| Mike Numbers | Finance | Finance Intern | Intern, All-Employees |
| Jamie Sales | Sales | Sales Representative | All-Employees, plus Helpdesk (see below) |

Entra ID has no organizational units like Active Directory does. The department is stored as an attribute on each user, and that attribute is what a review compares group membership against.

I seeded one problem on purpose: Jamie was also placed in `Helpdesk`, to practice finding excess access the same way I found it in the on-premises lab.

## Method
1. Opened each group's member list and noted who was in it.
2. Added the Department and Job title columns to the All users view, so each user's department sat next to their groups.
3. Compared each person's department and job with the groups they belonged to.

## Finding
Jamie Sales works in Sales but was a member of `Helpdesk`, an IT support role. Nothing in her department justified that access. It is the same privilege creep pattern as the on-premises review: access that does not match the job.

I also caught a documentation gap during the review. Jamie's Department field had been left blank when the account was created. A review compares department to groups, so a blank department makes the comparison meaningless. I corrected it before continuing.

## Remediation
Removed Jamie from the `Helpdesk` group in the group's Members list. Her account, her department, and her `All-Employees` membership stayed unchanged.

## Verification
- `Helpdesk` members: only Sarah Tech remains.
- Jamie's own group list: only `All-Employees`.

Checking from both sides, the group's members and the user's groups, confirms the change in both directions.

## Differences from the Active Directory review
| | Active Directory | Entra ID |
|---|---|---|
| Where the department lives | OU placement | Department attribute on the user |
| How I reviewed | PowerShell group queries | Admin center group and user views |
| Admin privilege issue | PowerShell had to be run as Administrator | Admin portal permissions applied automatically |

## Notes on scope
Entra ID has built-in access review campaigns and self-service password reset, but they require a paid Premium or Governance license. I did this review manually, which is also what makes the process clear. Trying the built-in feature on a trial is a possible later step.

## IAM principles
- Department describes who someone is and where they work. Group membership is what grants access. A review checks that the two agree.
- Access built up over time (privilege creep) is found by comparing current access with current role, not by checking that access was granted correctly.
- A review is not finished until the fix is made and proven from both the group's side and the user's side.
- Data quality matters: a missing department field breaks the comparison.
- In a real organization a manager would approve or reject each decision, and the outcome would be recorded for auditors.
