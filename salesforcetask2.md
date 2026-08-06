# 📘 Sprint 6 – Part II: Understanding How Triggers Think

> **"Automation becomes reliable when software knows exactly when to act and what responsibility belongs to it."**

## 📖 Overview

This section explains the design philosophy behind Apex Triggers. Instead of placing all business logic inside triggers, Salesforce applications follow a layered architecture where triggers observe business events and delegate business logic to service classes.

---

# 🎯 Learning Objectives

After completing this section, I learned to:

- Understand the responsibility of an Apex Trigger.
- Separate Trigger logic from Business logic.
- Differentiate between **Before** and **After** Trigger events.
- Design maintainable Trigger architecture.
- Understand how one business event can trigger multiple independent processes.

---

# 📚 Topics Covered

## 1️⃣ Trigger Responsibility

An Apex Trigger should **observe events**, not implement business logic.

### Trigger Responsibilities

- Detect business events.
- Call the appropriate Service Class.
- Keep the code lightweight.
- Delegate business processing.

---

## 2️⃣ Service Class Responsibility

Service classes contain all business logic.

Typical responsibilities include:

- Validate business rules.
- Execute SOQL queries.
- Perform DML operations.
- Send notifications.
- Update related records.
- Maintain reusable business logic.

---

## 🏗 Recommended Architecture

```text
Business Event
      │
      ▼
 Apex Trigger
      │
      ▼
 Service Class
      │
      ▼
 Business Logic
```

---

# 🔄 Trigger vs Service Class

| Trigger | Service Class |
|----------|---------------|
| Observes Events | Executes Business Logic |
| Calls Service Methods | Validates Business Rules |
| Lightweight | Performs SOQL & DML |
| One Trigger Per Object | Reusable Across Application |
| No Complex Logic | Maintains Business Processes |

---

# ⏱ Trigger Timing

Salesforce provides two primary execution timings.

## Before Trigger

Used when validation or data modification must occur before saving records.

### Examples

- Validate Student Eligibility
- Prevent Duplicate Applications
- Check Required Fields
- Enforce Business Rules

---

## After Trigger

Used after records have been successfully committed.

### Examples

- Send Confirmation Email
- Update Dashboards
- Create Related Records
- Notify Placement Officer
- Write Audit Logs

---

# 📋 Before vs After Trigger

| Business Requirement | Trigger Timing |
|----------------------|----------------|
| Validate Student Eligibility | Before |
| Prevent Duplicate Applications | Before |
| Reject Invalid Data | Before |
| Create Related Records | After |
| Send Email Notification | After |
| Update Reports | After |
| Refresh Dashboards | After |

---

# 🔗 One Event → Multiple Processes

A single business event can initiate several independent business operations.

### Example

**Student Selected**

Automatically:

- Update Placement Status
- Record Joining Company
- Notify Training Department
- Refresh Dashboards
- Send Congratulations Email
- Inform Alumni Office

Each responsibility belongs to a separate service, making the application modular and maintainable.

---

# 🛠 Design Principles

- Keep Triggers Small
- Use One Trigger Per Object
- Delegate Business Logic to Service Classes
- Separate Responsibilities
- Follow Layered Architecture
- Build Reusable Components

---

# 💻 Practical Architecture

```text
Student Selected
        │
        ▼
 Application Trigger
        │
        ▼
 Application Service
        │
        ├── Update Student Status
        ├── Notify Placement Officer
        ├── Send Email
        ├── Update Dashboard
        └── Record Audit Information
```

---

# 🚀 Benefits of This Architecture

- Clean Code
- Easy Maintenance
- Better Readability
- High Reusability
- Easy Testing
- Improved Scalability

---

# 🎯 Key Takeaways

- Triggers observe events rather than contain business logic.
- Service Classes implement business decisions.
- Business timing determines whether logic belongs in a Before or After Trigger.
- A single business event can initiate multiple independent business processes.
- Following Salesforce best practices results in maintainable and enterprise-ready applications.

---

# 📚 Skills Gained

- Apex Trigger Design
- Event-Driven Architecture
- Trigger Timing
- Before & After Trigger Concepts
- Service Class Design
- Layered Salesforce Architecture
- Enterprise Application Development

---

# 🏁 Conclusion

This section strengthened my understanding of professional Apex Trigger architecture by emphasizing separation of responsibilities, proper trigger timing, and reusable service classes. Following these principles enables the development of scalable, maintainable, and enterprise-grade Salesforce applications.
