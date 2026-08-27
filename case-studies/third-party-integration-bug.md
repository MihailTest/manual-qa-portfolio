# Manual QA Testing

## Third-Party Integration Bug Investigation

### Project Overview

This case study is based on a real issue I identified while testing a production web application.

The application depended on several third-party services. One of the main workflows allowed users to create service tickets that were submitted to an external ticketing platform.

For confidentiality reasons, all company, client, product, system, and technical identifiers have been removed or generalized.

### Workflow

The ticket creation process involved several steps:

1. A user created a ticket in the main web application.
2. During ticket creation, the user selected a location from an address dropdown powered by a location API.
3. The application submitted the completed ticket to a third-party ticketing system.
4. The third-party system created the ticket.
5. The main application retrieved the ticket details and displayed them to the user.

### The Problem

The main application accepted addresses containing certain special characters.

However, the third-party ticketing system had stricter validation rules for the same address field.

Because the validation rules were different between the two systems, a user could successfully complete the ticket creation form in the main application, but the ticket would fail when submitted to the third-party platform.

### How I Found It

One of our largest clients operated in areas close to the U.S.-Mexico border.

Because location data was important for their workflow, I started testing addresses relevant to that region.

While testing different locations and address formats, I found that some valid addresses containing special characters were accepted by our application but rejected by the third-party system during ticket submission.

### Related Bug Report

[Bug Report 001 - Ticket submission fails in third-party system when selected address contains unsupported special characters](../bug-reports/001-ticket-submission-special-characters.md)

### Investigation

I reproduced the issue using multiple address variations and compared successful and failed ticket submissions.

The failure occurred only with specific address formats containing characters that were accepted by the main application but rejected by the external system.

This showed that the issue was not caused by the general ticket creation workflow or the location lookup itself.

The problem was caused by inconsistent validation rules between the two integrated systems.

### Root Cause

The main application and the third-party ticketing platform applied different validation rules to the address field.

The main application accepted the value returned by the location API without validating whether that value was compatible with the third-party system.

### Business Impact

This issue affected a business-critical workflow because users could complete the entire ticket creation process, but the ticket would fail before being created in the external platform.

For clients operating in regions where these address formats were common, this could result in:

* Failed ticket submissions
* Additional manual work
* Delayed service requests
* Confusion for end users
* Reduced reliability of the integration

### Recommended Fix

Validate or normalize address values before sending them to the third-party platform.

The application should also handle validation errors returned by the external system and provide a clear message to the user instead of allowing the workflow to fail without useful feedback.

### QA Validation After Fix

After the fix, the workflow should be validated with:

* Standard addresses
* Addresses containing special characters
* Different regional address formats
* Previously failing locations
* Negative validation scenarios
* Successful ticket creation in the third-party system
* Correct retrieval of the created ticket details

### Skills Demonstrated

* Manual Testing
* Exploratory Testing
* Integration Testing
* Regression Testing
* Third-Party API Testing
* Defect Investigation
* Root Cause Analysis
* Risk-Based Testing
* Bug Reporting

### Tools and Areas Involved

* Web Application Testing
* Browser DevTools
* API / Integration Validation
* Third-Party Systems
* Location API
* Backend Workflow Investigation
* AWS CloudWatch

### Confidentiality Note

This case study is based on real professional QA experience. All client names, company names, product names, URLs, screenshots, identifiers, data, and proprietary implementation details have been removed or generalized to respect confidentiality obligations.
