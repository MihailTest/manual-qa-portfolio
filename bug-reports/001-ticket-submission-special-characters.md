# Bug Report 001

## High: Ticket submission fails in third-party system when selected address contains unsupported special characters

### Summary

The main application accepts certain addresses returned by the location lookup, but the downstream third-party ticketing platform rejects those values during ticket creation. The user can complete the workflow in the main application, yet the ticket fails after submission because the integrated system applies stricter address validation.

### Metadata

**Area:** Ticket Creation / Third-Party Integration  
**Severity:** High  
**Priority:** High  
**Type:** Integration / Validation  
**Environment:** QA web application with third-party ticketing integration  
**Frequency:** Reproducible with affected address formats  
**Reproducibility:** Consistent for addresses containing unsupported special characters  

### Preconditions

* User is authenticated.
* User has permission to create tickets.
* Third-party ticket integration is enabled and reachable.
* Location lookup is enabled in the ticket creation flow.
* Test data includes at least one address containing characters accepted by the main application but rejected by the third-party platform.

### Steps to Reproduce

1. Open the ticket creation workflow in the main application.
2. Enter all required ticket details.
3. Search for an address containing special characters in the location dropdown.
4. Select the affected address from the returned results.
5. Complete any remaining required fields.
6. Submit the ticket.
7. Observe the ticket creation result in the main application and in the integrated third-party platform.

### Expected Result

Only address values supported by both systems should be accepted in the workflow.

If the selected address is not compatible with the third-party platform, the user should receive a clear validation message before submission and should be prevented from sending an invalid request.

### Actual Result

The main application accepts the address and allows the user to submit the ticket.

The ticket then fails during downstream submission because the third-party platform rejects the address value.

### Evidence and Observations

* The issue reproduces with specific regional address formats containing unsupported special characters.
* Standard addresses without the affected characters are processed successfully.
* The failure occurs after the main application accepts the form input, which indicates a validation gap between integrated systems.
* The behavior suggests that the main application trusts the location API response without verifying compatibility with the ticketing platform.

### Business Impact

This issue breaks a business-critical workflow after the user has already completed the form, which increases operational risk and user frustration.

Potential impact includes:

* Failed ticket submissions
* Additional manual correction or resubmission work
* Delayed service handling
* Reduced trust in the integration
* Poor user experience for clients operating in affected regions

### Suspected Root Cause

The main application and the third-party ticketing system enforce different validation rules for the address field. The application accepts the location API value without confirming that the downstream platform can process the same value.

### Recommended Fix

Validate or normalize address values before sending them to the third-party platform.

The application should also surface third-party validation failures as actionable user feedback instead of allowing the workflow to fail silently or too late in the process.

### Regression Scope

Validate the fix against:

* Standard address formats
* Previously failing addresses with special characters
* Multiple regional address patterns
* Negative validation scenarios
* Successful ticket creation and retrieval in the third-party platform

### Related Case Study

[Third-Party Integration Bug Investigation](../case-studies/third-party-integration-bug.md)
