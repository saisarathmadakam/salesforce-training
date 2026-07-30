# Salesforce Interview Readiness Bootcamp – Day 3 Assignment

## Student Details

**Name:** Madakam Sai Sarath

**Project:** Placement Management System Automation

---

# Project Overview

This project enhances the Placement Management System using Salesforce declarative automation. The solution was implemented using Custom Objects, Record-Triggered Flows, Validation Rules, and Email Notifications to automate the placement application process while maintaining data quality.

---

# Custom Objects Created

1. Student
2. Job
3. Application
4. Offer Letter

---

# Business Requirements Implemented

## Requirement 1
Automatically populate the Application Date whenever a new Application record is created.

**Solution Used:**
- Record-Triggered Flow (Before Save)

**Reason:**
Before Save Flow provides faster performance and is ideal for updating fields before the record is saved.

---

## Requirement 2

Send an Email Notification to the Placement Officer whenever a new Application is submitted.

**Solution Used:**
- Record-Triggered Flow (After Save)

**Reason:**
Email notifications require the record to be saved first. Therefore, an After Save Flow was used.

---

## Requirement 3

Reject applications when the student's CGPA is lower than the Job's Minimum CGPA.

**Solution Used**
- Validation Rule

**Reason**
Validation Rules prevent invalid data from being saved and maintain data quality.

---

## Requirement 4

Application Date should not be later than the Job Closing Date.

**Solution Used**
- Validation Rule

---

## Requirement 5

Mandatory fields cannot be left blank.

**Solution Used**
- Validation Rule

---

# Validation Rules Implemented

### 1. CGPA Validation

Purpose:
Prevent students with lower CGPA from applying.

Formula:

Student__r.CGPA__c < Job__r.Minimum_CGPA__c

---

### 2. Application Date Validation

Purpose:
Prevent applications after the Job Closing Date.

Formula:

Application_Date__c > Job__r.Closing_Date__c

---

### 3. Mandatory Fields Validation

Purpose:
Ensure required fields are entered before saving.

Formula:

OR(
ISBLANK(Student__c),
ISBLANK(Job__c),
ISPICKVAL(Status__c,"")
)

---

# Flow Implementation

## Flow 1

Name:
Application Date Flow

Type:
Record Triggered Flow (Before Save)

Purpose:
Automatically populate the Application Date.

---

## Flow 2

Name:
Application Confirmation Email Flow

Type:
Record Triggered Flow (After Save)

Purpose:
Send email notification to the Placement Officer whenever a new application is created.

---

# Successful Execution

The following functionalities were successfully tested.

- Application Date populated automatically.
- Email notification sent successfully.
- CGPA Validation Rule working.
- Application Date Validation Rule working.
- Mandatory Field Validation working.

---

# Validation Rule Formulas

CGPA Validation

Student__r.CGPA__c < Job__r.Minimum_CGPA__c

Application Date Validation

Application_Date__c > Job__r.Closing_Date__c

Mandatory Fields Validation

OR(
ISBLANK(Student__c),
ISBLANK(Job__c),
ISPICKVAL(Status__c,"")
)

---

# Flow vs Validation Rule vs Apex

## Requirements solved using Flow

- Auto-fill Application Date
- Send Email Notification

---

## Requirements solved using Validation Rules

- Reject low CGPA
- Validate Application Date
- Prevent blank mandatory fields

---

## Requirements that may require Apex

- Prevent duplicate applications
- Automatically create Offer Letter records when Status changes to Selected
- Complex business logic
- Bulk data processing
- External REST API integrations

---

# Why These Solutions Were Chosen

Flow was selected because it provides declarative automation without writing code and is recommended by Salesforce for most automation tasks.

Validation Rules were used to validate user input before saving records.

Apex should only be used when declarative tools cannot satisfy the business requirement or when advanced logic, integrations, or large-scale processing is required.

---

# Technologies Used

- Salesforce CRM
- Flow Builder
- Validation Rules
- Custom Objects
- Email Actions

---

# Project Deliverables

✔ Custom Objects

✔ Record Triggered Flows

✔ Validation Rules

✔ Email Notification

✔ Successful Execution Screenshots

✔ Validation Rule Formulas

✔ GitHub Repository

✔ README Documentation

---

# GitHub Repository

Repository Link:

https://github.com/YourUsername/Salesforce-Day3-Assignment

---

# Conclusion

The Placement Management System was successfully enhanced using Salesforce declarative automation. Record-Triggered Flows automated application processing, Validation Rules ensured data integrity, and Email Notifications improved communication. This implementation follows Salesforce best practices by using declarative tools wherever possible and minimizing the need for Apex.

---

# Author

Madakam Sai Sarath

B.Tech CSE (Cyber Security)

Vishnu Institute of Technology
