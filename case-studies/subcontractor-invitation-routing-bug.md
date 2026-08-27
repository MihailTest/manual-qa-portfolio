# Manual QA Testing

## Subcontractor Invitation Routing Bug Investigation

### Project Overview

This case study is based on a real issue I identified while testing a web application in the QA environment.

The platform connected contractors with subcontractors and allowed contractors to invite subcontractors to jobs.

The invitation flow depended on whether the subcontractor already had an account:

* Existing subcontractor - receives the job invitation directly inside the platform.
* External subcontractor - does not have an account yet and receives the job invitation by email.

External subcontractors could later register and become platform users. Once registered, future job invitations should be delivered directly through the platform instead of following the external email-only flow.

For confidentiality reasons, all company, client, product, system, database, and technical identifiers have been removed or generalized.

### Workflow

The expected invitation workflow was:

1. A contractor selects a subcontractor and invites them to a job.
2. The system determines whether the subcontractor has an active platform account.
3. If the subcontractor is an existing platform user, the invitation is created and displayed inside the platform.
4. If the subcontractor is external, the invitation is sent to their email address.
5. An external subcontractor can later create a platform account.
6. The system should associate the new account with the existing subcontractor record.
7. Future job invitations should then appear directly inside the platform.

### The Problem

I found that an external subcontractor who later created a platform account was still treated as an external subcontractor by the invitation workflow.

The account creation itself was successful. The subcontractor could log in and use the platform.

However, when a contractor sent a new job invitation:

* The subcontractor received the invitation by email.
* No invitation appeared inside the platform.

This was the same behavior expected for a subcontractor without an account.

### How I Found It

I discovered the issue during testing in the QA environment while validating the subcontractor invitation lifecycle.

Instead of testing only existing and external subcontractors independently, I tested the transition between both states:

External subcontractor -> Account creation -> New job invitation

After the external subcontractor created an account, I sent another job invitation and noticed that it was still delivered by email and was missing from the platform.

There was no visible application error, and both account creation and invitation sending appeared successful.

This indicated that the problem was likely related to how the backend identified the subcontractor rather than the invitation UI itself.

### Related Bug Report

[Bug Report 002 - Registered subcontractor continues receiving invitations through external email flow after account creation](../bug-reports/002-subcontractor-invitation-routing.md)

### Investigation

I reproduced the issue with the external-to-registered subcontractor workflow and compared it with a subcontractor who already had an account.

The invitation functionality worked correctly for users who had been registered platform subcontractors from the beginning.

The issue occurred specifically when an existing external subcontractor later created an account.

There were no useful errors in the UI, and the individual operations appeared successful:

* Account creation succeeded.
* Login succeeded.
* Job invitation creation succeeded.
* Email delivery succeeded.

I therefore investigated the underlying data using SQL queries.

I compared the subcontractor records and identifiers for a correctly working registered subcontractor and the affected subcontractor.

The investigation showed that the external subcontractor already had an existing database ID before registration. When the subcontractor later created an account, the system associated the account with the existing record rather than creating a new subcontractor identity.

However, the invitation logic continued identifying that record as an external subcontractor.

This explained why the system continued routing invitations through email instead of making them available inside the platform.

### Root Cause

The external-to-registered subcontractor transition was not handled consistently by the backend.

The subcontractor's existing record and identifier remained associated with the external subcontractor state after account creation.

The invitation routing logic relied on this underlying subcontractor data to determine whether an invitation should be:

* delivered inside the platform, or
* sent through the external email flow.

As a result, the newly registered subcontractor continued being processed as external even though a valid platform account now existed.

### Business Impact

This issue affected subcontractors who initially interacted with the company externally and later decided to join the platform.

The registration appeared successful, but one of the platform's important workflows did not transition with the user.

This could result in:

* Job invitations missing from the platform
* Registered users continuing to depend on email notifications
* Confusion about whether an invitation was successfully created
* Missed or delayed job opportunities
* Inconsistent user experience
* Increased support requests
* Reduced value of creating a platform account

### Recommended Fix

The account registration workflow should correctly transition an existing external subcontractor into a registered platform subcontractor.

After successful account creation, the backend should ensure that:

* The new account is correctly associated with the existing subcontractor record.
* The subcontractor is no longer classified as external.
* Invitation routing recognizes the subcontractor as a platform user.
* Future invitations are associated with the registered account and visible inside the platform.
* Existing historical data remains correctly associated with the subcontractor.

Additional logging around subcontractor identification and invitation routing would also make similar issues easier to investigate.

### QA Validation After Fix

After the fix, I would validate:

* Invitation to an existing registered subcontractor
* Invitation to an external subcontractor
* External subcontractor registration
* New invitation after external-to-registered transition
* Invitation visibility inside the platform
* Email behavior before and after registration
* Correct subcontractor/account association in the database
* Multiple invitations before and after registration
* Existing registered subcontractor regression
* External subcontractor regression
* API and database state after account creation
* Duplicate email/company matching scenarios

### Skills Demonstrated

* Manual Testing
* Exploratory Testing
* Backend Testing
* Database Testing
* SQL
* End-to-End Workflow Testing
* State Transition Testing
* Integration Testing
* Regression Testing
* Defect Investigation
* Root Cause Analysis
* Data Validation
* Risk-Based Testing
* Bug Reporting

### Tools and Areas Involved

* Web application testing
* SQL/database queries
* Browser DevTools
* API/backend validation
* User registration
* Job invitation workflow
* Email notifications
* Account/subcontractor association
* Backend business logic investigation

### Confidentiality Note

This case study is based on real professional QA experience discovered and investigated in a QA environment. All client names, company names, product names, URLs, database identifiers, table names, IDs, data, and proprietary implementation details have been removed or generalized to respect confidentiality obligations.
