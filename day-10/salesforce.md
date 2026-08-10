# Salesforce LWC – Chapter 10

## Component Communication, Forms, LDS and Reusable LWC Architecture

---

# 1. Introduction

A real Salesforce application is not made from one large LWC.

Instead, it is built using multiple focused components that communicate with each other.

For example:

```text
StudentPortal
│
├── StudentSummary
├── StudentProfile
├── EligibleJobs
│   └── JobCard
├── MyApplications
│   └── ApplicationCard
└── OfferSummary
```

Each component should have a clear responsibility.

The main goal of this chapter is to understand:

* LWC component architecture
* Parent → Child communication
* Child → Parent communication
* `@api`
* Custom Events
* Event contracts
* Lightning Base Components
* Forms
* Client-side validation
* Server-side validation
* Lightning Data Service (LDS)
* Reactive data
* Refreshing data
* Loading, success, empty and error states
* Reusable components
* Empty State components
* Avoiding God Components
* Complete LWC application flow

---

# 2. Component Architecture

## What is Component Architecture?

Component architecture means dividing an application into smaller components where each component has a specific responsibility.

Instead of:

```text
StudentPortal
   └── Everything
```

we create:

```text
StudentPortal
│
├── StudentSummary
├── StudentProfile
├── EligibleJobs
│   └── JobCard
├── MyApplications
│   └── ApplicationCard
└── OfferSummary
```

### Principle

> A component should have a clear responsibility and a clear relationship with other components.

A good application is:

```text
Focused Components
       ↓
Clear Communication
       ↓
One Coherent Application
```

---

# 3. Why Not One Giant Component?

Imagine one component contains:

```text
Job Search
Job Details
Application
Withdraw
Interview
Offer
Profile
Notifications
Admin Actions
```

Initially this may work.

But eventually the component contains:

* Hundreds of lines
* Many conditions
* Many events
* Too much state
* Too many responsibilities

This becomes difficult to maintain.

This type of architecture is commonly called a:

## God Component

A God Component is an overly large component that controls too many responsibilities.

### Better approach

```text
StudentPortal
│
├── StudentProfile
├── EligibleJobs
│   └── JobCard
└── MyApplications
    └── ApplicationCard
```

The parent coordinates, while children handle focused responsibilities.

---

# 4. Parent → Child Communication

This is one of the most important LWC concepts.

When:

```text
Parent has data
       ↓
Child needs data
```

we use a public property with:

```javascript
@api
```

---

# 5. Parent → Child Example

Suppose the parent has:

```javascript
selectedJob = {
    company: 'Google',
    role: 'Java Developer',
    package: '12 LPA'
};
```

The parent HTML:

```html
<c-job-card
    job={selectedJob}>
</c-job-card>
```

The child JavaScript:

```javascript
import { LightningElement, api } from 'lwc';

export default class JobCard extends LightningElement {

    @api job;

}
```

The child can then use:

```html
<p>{job.company}</p>
<p>{job.role}</p>
<p>{job.package}</p>
```

### Flow

```text
StudentPortal
     |
     | selectedJob
     ↓
JobCard
     |
     | @api job
     ↓
Display Job
```

### Important

Parent → Child:

```text
@api
```

---

# 6. Complete Parent → Child Example

## studentPortal.js

```javascript
import { LightningElement } from 'lwc';

export default class StudentPortal extends LightningElement {

    selectedJob = {
        company: 'Google',
        role: 'Java Developer',
        package: '12 LPA'
    };

}
```

## studentPortal.html

```html
<template>

    <lightning-card title="Student Portal">

        <c-job-card
            job={selectedJob}>
        </c-job-card>

    </lightning-card>

</template>
```

## jobCard.js

```javascript
import { LightningElement, api } from 'lwc';

export default class JobCard extends LightningElement {

    @api job;

}
```

## jobCard.html

```html
<template>

    <lightning-card title="Job Details">

        <p>Company: {job.company}</p>

        <p>Role: {job.role}</p>

        <p>Package: {job.package}</p>

    </lightning-card>

</template>
```

Output:

```text
Job Details

Company: Google
Role: Java Developer
Package: 12 LPA
```

---

# 7. Child → Parent Communication

Now consider the opposite situation.

The child knows something happened.

The parent needs to know.

Example:

```text
JobCard
   |
   | Apply clicked
   ↓
StudentPortal
```

A child should not directly manipulate the parent's state.

Instead, the child sends a:

## Custom Event

---

# 8. Custom Event

A child can dispatch an event:

```javascript
this.dispatchEvent(
    new CustomEvent('viewdetails', {
        detail: {
            jobId: this.job.Id
        }
    })
);
```

The parent listens:

```html
<c-job-card
    job={job}
    onviewdetails={handleViewDetails}>
</c-job-card>
```

Parent JavaScript:

```javascript
handleViewDetails(event) {

    const jobId = event.detail.jobId;

    // Parent decides what to do
}
```

---

# 9. Child → Parent Flow

```text
User clicks button
       ↓
Child
       ↓
dispatchEvent()
       ↓
Custom Event
       ↓
Parent
       ↓
Event Handler
       ↓
Parent decides what to do
```

Remember:

```text
Parent → Child
      @api

Child → Parent
      Custom Event
```

---

# 10. Why Shouldn't Child Directly Modify Parent?

Bad design:

```text
Child
  ↓
Directly changes Parent state
```

This creates tight coupling.

The child may start modifying:

* Selected Job
* Application state
* Filters
* Navigation
* Notifications

This makes the architecture difficult to understand and maintain.

Better design:

```text
Child:
"Something happened."

       ↓

Parent:
"I decide what that means."
```

### Important interview principle

> Children report events. Parents coordinate behaviour.

---

# 11. Event Contract

An event should clearly communicate what actually happened.

Suppose we use:

```text
apply
```

What does that mean?

Possibility 1:

```text
User clicked Apply.
```

Possibility 2:

```text
Application was successfully created.
```

These are not the same.

If the child only knows that the user clicked the button, a better event name is:

```text
applyclicked
```

rather than:

```text
applicationsubmittedsuccessfully
```

because the child does not yet know whether Apex/database processing succeeded.

---

# 12. Intent vs Outcome

Always distinguish:

```text
Intent
   ↓
Outcome
```

Example:

```text
User clicks Apply
       ↓
Intent
       ↓
Apex
       ↓
Database
       ↓
Success / Failure
       ↓
Outcome
```

Good event:

```text
applyclicked
```

Potentially incorrect event:

```text
applicationsubmittedsuccessfully
```

unless the child actually knows that the operation succeeded.

### Principle

> Good event design communicates facts. Bad event design communicates assumptions.

---

# 13. Lightning Base Components

Salesforce provides standard Lightning components for common UI requirements.

Examples:

```text
lightning-button
lightning-card
lightning-input
lightning-combobox
lightning-textarea
lightning-checkbox-group
lightning-radio-group
```

Instead of recreating common UI elements manually, use Salesforce Lightning base components when they satisfy the requirement.

### Principle

> Reuse the platform before reinventing the platform.

---

# 14. Forms in LWC

Suppose a Student Profile contains:

```text
Name
Phone
Email
Branch
CGPA
Skills
Preferred Location
```

We can build the form using Lightning base components.

Example:

```html
<lightning-input
    label="Phone"
    value={phone}
    onchange={handlePhoneChange}>
</lightning-input>
```

Email:

```html
<lightning-input
    type="email"
    label="Email"
    value={email}
    onchange={handleEmailChange}>
</lightning-input>
```

---

# 15. Handling Form Changes

JavaScript:

```javascript
handlePhoneChange(event) {
    this.phone = event.target.value;
}

handleEmailChange(event) {
    this.email = event.target.value;
}
```

Flow:

```text
User enters value
       ↓
onchange
       ↓
Event Handler
       ↓
JavaScript property updated
```

---

# 16. Client-Side Validation

Client-side validation happens in the browser.

Example:

CGPA should be between:

```text
0 and 10
```

JavaScript can check:

```javascript
if (cgpa >= 0 && cgpa <= 10) {
    // Valid
}
```

The purpose is mainly:

```text
Better User Experience
```

For example, the user can immediately see:

```text
CGPA must be between 0 and 10.
```

---

# 17. Server-Side Validation

Client-side validation cannot be trusted as the only protection.

A user or another client could potentially bypass the browser.

Therefore, important business rules should also be enforced on the server.

```text
Client Validation
       ↓
Better User Experience

Server Validation
       ↓
Business Integrity
```

### Important interview question

**Why is client-side validation not sufficient?**

Answer:

> Client-side validation improves user experience, but it cannot be trusted for business security because the client can be bypassed. Important business rules must remain enforced server-side.

---

# 18. Client vs Server Validation

| Validation  | Purpose            |
| ----------- | ------------------ |
| Client-side | User experience    |
| Server-side | Business integrity |

Example:

```text
CGPA 0–10
```

Client:

```text
Show immediate error
```

Server:

```text
Enforce business rule
```

Both can be used because they have different responsibilities.

---

# 19. Lightning Data Service (LDS)

Suppose we need to:

* Retrieve a record
* Update a record
* Perform basic record operations

Do we always need Apex?

## No.

Lightning Data Service can provide standard mechanisms for working with supported Salesforce records.

Therefore, custom Apex may not always be necessary.

---

# 20. LDS vs Apex

### Consider LDS when:

* Standard record operations are sufficient.
* Salesforce platform capabilities already provide what you need.
* You want to avoid unnecessary custom server-side code.

### Consider Apex when:

* Complex business logic is required.
* Custom server-side processing is needed.
* Complex SOQL/DML processing is required.
* The requirement cannot appropriately be handled by LDS.

### Important principle

Do not choose Apex simply because:

```text
"We know Apex."
```

Instead:

```text
Requirement
    ↓
Architecture Decision
    ↓
LDS / Apex / Combination
```

---

# 21. Reactive Data

Suppose:

```text
Student CGPA
7.4 → 8.2
```

This change can affect:

```text
Student Summary
Eligible Jobs
Application Eligibility
```

Now the application must consider which components need updated information.

This is reactive data thinking.

---

# 22. Data Ownership

Ask:

> Who owns this data?

Suppose:

```text
StudentSummary
CGPA = 8.1

EligibleJobs
CGPA = 7.9
```

Now the user sees contradictory information.

The problem is not only a UI bug.

It is an architectural/data ownership problem.

### Principle

> Data ownership must be clear.

Avoid having many components maintain unrelated copies of the same changing information without a good reason.

---

# 23. Stale Data

Example:

Initially:

```text
CGPA = 7.4
```

Student updates it:

```text
CGPA = 8.2
```

Profile saves successfully.

But Eligible Jobs still shows opportunities based on:

```text
CGPA = 7.4
```

The Salesforce data is correct.

The UI is stale.

---

# 24. How to Handle Refresh

Depending on the architecture, possible strategies include:

* Parent-owned state
* Custom events
* Refreshing appropriate wired data
* LDS-supported record notifications/reactive updates
* Re-querying data when genuinely necessary

Do not create a complicated global state system unless the architecture actually requires it.

Start with the simplest approach that keeps the UI consistent.

---

# 25. Loading State

When data is being loaded:

```text
Loading your profile...
```

The user should know that the application is working.

---

# 26. Editing State

When the form is available:

```text
Normal Profile Form
```

The user can edit values.

---

# 27. Saving State

When the save operation is running:

```text
Saving...
```

This prevents the user from wondering whether their action worked.

---

# 28. Success State

After successful save:

```text
Profile updated successfully.
```

---

# 29. Error State

If something fails:

```text
We could not update your profile.

Please review the highlighted fields.
```

### Four important states

```text
Loading
   ↓
Editing
   ↓
Saving
   ↓
Success / Error
```

---

# 30. Reusable Components

Suppose three components display application/interview/offer status.

Instead of writing the same status UI three times, create:

```text
StatusBadge
```

Then:

```text
ApplicationCard
      ↓
StatusBadge

InterviewCard
      ↓
StatusBadge

OfferCard
      ↓
StatusBadge
```

One implementation can have multiple consumers.

---

# 31. What Makes a Good Reusable Component?

A reusable component should provide a meaningful capability.

Good names:

```text
StatusBadge
ApplicationStatus
JobCard
EmptyState
LoadingIndicator
```

Bad example:

```text
SmallBlueBox
```

The component name should communicate its purpose.

---

# 32. Reuse vs Over-Engineering

Reuse is useful when:

```text
Same meaningful functionality
+
Multiple consumers
```

But too much abstraction can create complexity.

For example, if a component is used only once and has very simple markup, creating an elaborate reusable abstraction may not be necessary.

### Principle

> Reuse reduces duplication, but unnecessary abstraction creates complexity.

---

# 33. Empty State Component

Suppose no eligible jobs are available.

Instead of:

```text
No records found.
```

a better empty state is:

```text
No eligible opportunities are available right now.

Keep your profile updated and check again
as new companies are added.

[UPDATE PROFILE]
```

This can become:

```text
EmptyState
```

---

# 34. EmptyState Example

Parent:

```html
<c-empty-state
    title="No Eligible Jobs"
    message="Check again when new opportunities are added.">
</c-empty-state>
```

The component can accept:

```text
title
message
optional action label
```

If the action button is clicked:

```text
EmptyState
    ↓
Custom Event
    ↓
Parent
```

Again, the same Child → Parent communication pattern is used.

---

# 35. Sibling Components

Suppose we have:

```text
StudentProfile
EligibleJobs
```

They are siblings.

Avoid directly making:

```text
StudentProfile → directly controls → EligibleJobs
```

Instead:

```text
          StudentPortal
          /           \
         ↓             ↓
StudentProfile    EligibleJobs
```

The common parent can coordinate the communication.

Example:

```text
StudentProfile
      ↓
Custom Event
      ↓
StudentPortal
      ↓
Refresh EligibleJobs
```

---

# 36. Complete Component Architecture

```text
StudentPortal
│
├── StudentSummary
│
├── StudentProfile
│
├── EligibleJobs
│   │
│   ├── JobCard
│   └── EmptyState
│
├── MyApplications
│   │
│   ├── ApplicationCard
│   └── EmptyState
│
└── OfferSummary
    │
    └── StatusBadge
```

---

# 37. Communication Rules

## Parent → Child

Use:

```text
@api
```

Example:

```text
Parent
  ↓
job={selectedJob}
  ↓
Child
  @api job
```

---

## Child → Parent

Use:

```text
Custom Event
```

Example:

```text
Child
  ↓
dispatchEvent()
  ↓
Parent handler
```

---

## Sibling → Sibling

Prefer:

```text
Common Parent
```

The parent coordinates the interaction.

---

# 38. Data Strategy

When designing an LWC application, decide where data should come from.

Possible approaches:

```text
LDS
Wire
Imperative Apex
```

The important question is not:

> "Which technology do I know?"

The important question is:

> "Which approach fits this requirement?"

---

# 39. Business Logic

Business rules should remain authoritative on the server.

Example:

```text
Student CGPA >= 7.5
        ↓
Eligible for Job
```

Even if JavaScript checks it, Apex/server-side logic should enforce important business rules.

---

# 40. Complete Student Placement Flow

The complete application journey:

```text
Student Login
      ↓
Student Summary
      ↓
Update Profile
      ↓
Profile Saved
      ↓
Eligible Jobs Refresh
      ↓
Select Job
      ↓
Job Details
      ↓
Apply
      ↓
Application Created
      ↓
My Applications Refresh
      ↓
Student Sees New Status
```

This is no longer just an isolated coding exercise.

It is an application workflow.

---

# 41. End-to-End Architecture

A Salesforce application can be thought of as:

```text
User
 ↓
LWC
 ↓
Apex
 ↓
Service
 ↓
Database
 ↓
Trigger / Automation
 ↓
Result
 ↓
LWC Refresh
 ↓
User
```

The goal is to understand the complete journey.

---

# 42. Example: Student Applies for a Job

### Step 1

Student clicks:

```text
Apply
```

### Step 2

`JobCard` knows the button was clicked.

### Step 3

Child sends:

```text
Custom Event
```

### Step 4

Parent receives the event.

### Step 5

Parent calls appropriate Apex/service logic.

### Step 6

Server validates business rules.

### Step 7

Application record is created.

### Step 8

UI refreshes.

### Step 9

Student sees the new application status.

---

# 43. Interview Questions and Answers

## Q1. How does a parent communicate with a child?

**Answer:**

A parent communicates data to a child through public properties exposed using `@api`.

---

## Q2. How does a child communicate with a parent?

**Answer:**

A child communicates with its parent using Custom Events. The child dispatches the event and the parent handles it.

---

## Q3. What is `@api`?

**Answer:**

`@api` exposes a property or method publicly so that the parent component can interact with the child.

---

## Q4. What are Custom Events?

**Answer:**

Custom Events allow a child component to communicate information or user actions to its parent.

---

## Q5. Why shouldn't a child directly modify parent state?

**Answer:**

Direct modification creates tight coupling. The child should report what happened through an event, while the parent decides what action should be taken.

---

## Q6. What is an Event Contract?

**Answer:**

An Event Contract defines what an event actually communicates. The event should accurately represent a fact or action rather than make assumptions about an outcome.

---

## Q7. Why is `applyclicked` better than `applicationsubmittedsuccessfully` in some cases?

**Answer:**

Because the child may only know that the user clicked Apply. It may not know whether the application was successfully created by Apex and Salesforce.

---

## Q8. When would you use LDS instead of Apex?

**Answer:**

When standard Salesforce record operations provided by LDS satisfy the requirement. Custom Apex is appropriate when complex custom server-side logic is required.

---

## Q9. Why is client-side validation not enough?

**Answer:**

Client-side validation improves user experience but can be bypassed. Important business rules must also be enforced server-side.

---

## Q10. What is reactive data?

**Answer:**

Reactive data means that dependent UI or data can update when the underlying information changes.

---

## Q11. Why might an LWC show stale information?

**Answer:**

The Salesforce record may have been updated, but the component displaying dependent information may not have refreshed or reacted to the change.

---

## Q12. What is a reusable component?

**Answer:**

A reusable component provides a meaningful capability that can be used by multiple consumers.

---

## Q13. What is a God Component?

**Answer:**

A God Component is an overly large component that handles too many responsibilities, states and behaviours.

---

## Q14. How should sibling components communicate?

**Answer:**

They should generally communicate through a common parent or another appropriate communication mechanism rather than directly manipulating each other.

---

## Q15. What are the important UI states?

**Answer:**

Loading, editing/normal, saving, success, empty and error states should be handled appropriately.

---

# 44. Important Interview Scenario

### Question

Two LWCs are on the same page.

One updates a student's CGPA.

The other displays eligible jobs.

How do you ensure the eligible jobs reflect the new CGPA?

### Strong answer

First identify:

```text
Who owns the Student state?
```

Then determine:

```text
How is the update communicated?
```

Then consider:

```text
Can LDS/reactive data handle the change?
```

If appropriate:

```text
Refresh wired data
```

or use:

```text
Custom Events
```

or another architecture-appropriate mechanism.

The important point is to reason about:

```text
Data ownership
Communication
Refresh
Consistency
```

rather than immediately naming an API.

---

# 45. Definition of Done

For the Student Placement Portal:

```text
✓ Student can view profile
✓ Student can update profile
✓ Profile validation works
✓ Eligible Jobs reflect current student information
✓ Job Cards are reusable
✓ Child components communicate through events
✓ Parents deliberately pass information to children
✓ Application submission works
✓ Duplicate applications are handled
✓ My Applications reflects new applications
✓ Loading states are visible
✓ Empty states are meaningful
✓ Errors are handled professionally
✓ Business rules remain server-side
✓ Components have clear responsibilities
✓ Complete data flow can be explained
```

---

# 46. Final Revision Diagram

Memorize this:

```text
                  LWC APPLICATION
                        |
          ┌─────────────┴─────────────┐
          ↓                           ↓
   Parent → Child              Child → Parent
       @api                    Custom Event
          |                           |
          ↓                           ↓
      Data Flow                  Event Flow
          |
          ↓
     Data Strategy
      /    |     \
    LDS   Wire   Apex
          |
          ↓
     Validation
      /       \
  Client     Server
    |           |
    UX       Business
            Integrity
          |
          ↓
      UI States
   /    |    |    \
Loading Empty Success Error
          |
          ↓
   Reusable Components
      /          \
 StatusBadge   EmptyState
```

---

# 47. The 10 Things You MUST Remember

```text
1. Component = focused responsibility

2. Parent → Child = @api

3. Child → Parent = Custom Event

4. Children report events

5. Parents coordinate behaviour

6. Client validation = better UX

7. Server validation = business integrity

8. Use LDS when it fits the requirement

9. Keep data ownership clear

10. Avoid God Components
```

---

# 48. Telugu-English Quick Revision

### Parent → Child

Parent daggara data undi.

Child ki kavali.

```text
Parent
  ↓
@api
  ↓
Child
```

### Child → Parent

Child lo user action jarigindi.

Parent ki cheppali.

```text
Child
  ↓
Custom Event
  ↓
Parent
```

### Validation

```text
Client → User experience
Server → Business rules
```

### LDS

Simple Salesforce record operations ki LDS consider cheyyali.

Complex logic unte Apex consider cheyyali.

### Reactive Data

Data change ayithe dependent components old data chupinchakunda appropriate refresh/update mechanism use cheyyali.

### Reusable Component

Same meaningful functionality multiple places lo use chesthe reusable component create cheyyachu.

### Main Idea

```text
Good Components
      +
Clear Communication
      +
Clear Responsibilities
      =
Good LWC Application
```

---

# 49. Final Interview Statement

If an interviewer asks:

**"Explain your LWC architecture."**

You can say:

> "I designed the LWC application using focused components with clear responsibilities. Parent components pass required data to children through `@api`, while children communicate user actions to parents using Custom Events. I use Lightning Data Service when standard record operations are sufficient and Apex when custom server-side logic is required. Client-side validation is used for user experience, while important business rules remain enforced on the server. I also consider data ownership, refresh behaviour, loading and error states, and reusable components to keep the application maintainable."

This is the core architecture of Chapter 10.
