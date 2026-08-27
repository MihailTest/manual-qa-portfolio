# Bug Report 002

## High: Registered subcontractor continues receiving job invitations through external email flow after account creation

### Summary

An external subcontractor who later registers successfully as a platform user continues to be treated as an external contact by the invitation routing logic. New job invitations are still sent by email and do not appear inside the platform, even though the subcontractor account is active.

### Metadata

**Area:** Subcontractor Management / Job Invitations  
**Severity:** High  
**Priority:** High  
**Type:** Backend / Data / Business Logic  
**Environment:** QA web application environment  
**Frequency:** Reproducible for the external-to-registered subcontractor transition  
**Reproducibility:** Consistent in the tested workflow  
**Reported By:** Manual QA

### Preconditions

* An external subcontractor exists in the system without a platform account.
* A contractor account can invite subcontractors to jobs.
* The external subcontractor can register using the same company details tied to the existing record.
* Email delivery for external subcontractor invitations is operational.

### Steps to Reproduce

1. Add or select a subcontractor that currently exists only as an external contact.
2. Send a job invitation to confirm the external flow is working through email.
3. Register a platform account for the same subcontractor.
4. Sign in as the subcontractor and confirm the account was created successfully.
5. Sign back in as the contractor.
6. Send a new job invitation to the same subcontractor.
7. Sign in as the subcontractor and check invitations inside the platform.
8. Check the subcontractor email inbox.

### Expected Result

After successful registration, the subcontractor should be recognized as a platform user.

Future invitations for that subcontractor should be associated with the registered account and appear directly inside the platform, rather than continuing through the external email-only flow.

### Actual Result

The new invitation is not available inside the platform for the registered subcontractor.

The invitation continues to be sent by email, which matches the external subcontractor workflow instead of the registered user workflow.

### Evidence and Observations

* Account creation succeeds.
* Login succeeds for the new subcontractor account.
* Invitation creation succeeds from the contractor side.
* Email delivery succeeds.
* The defect appears specifically after the state transition from external subcontractor to registered platform user.
* Comparison with subcontractors who were registered from the beginning shows expected behavior only for those records.

### Business Impact

This issue breaks an important post-registration workflow and reduces the value of joining the platform for previously external subcontractors.

Potential impact includes:

* Invitations missing from the in-platform experience
* Continued dependence on email notifications for registered users
* Confusion about invitation status
* Missed or delayed job opportunities
* Increased support effort
* Inconsistent behavior between user cohorts

### Suspected Root Cause

The backend does not fully transition the subcontractor record from external state to registered platform state after account creation. Invitation routing continues using the existing underlying record as if it still belongs to the external-only flow.

### Recommended Fix

Update the registration and invitation routing logic so that a subcontractor who creates an account is fully recognized as a registered platform user.

The backend should ensure that:

* The account is correctly associated with the existing subcontractor record.
* The subcontractor is no longer classified as external after successful registration.
* Invitation routing targets the platform account for future invites.
* Historical associations remain intact.

### Regression Scope

Validate the fix against:

* Invitations to already registered subcontractors
* Invitations to external subcontractors
* External subcontractor registration
* Invitations sent after registration
* Email behavior before and after transition
* Platform visibility of new invitations
* Database and API state for account association
* Duplicate email or company matching edge cases

### Related Case Study

[Subcontractor Invitation Routing Bug Investigation](../case-studies/subcontractor-invitation-routing-bug.md)
