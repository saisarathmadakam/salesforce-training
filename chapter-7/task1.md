# Sprint 7 – Bulk Processing and Governor Limits

## 📌 Objective

Learn how to build scalable Apex applications that can process multiple records efficiently while respecting Salesforce Governor Limits.

---

## 📖 Concepts Covered

### 7.1 The Day Our Perfect Trigger Failed

**Concept**
- Code that works for one record may fail when processing 200 records.
- Enterprise applications must be designed for scale.
- A correct query does not always mean a correct design.

**Key Learning**
- Always design Apex to handle bulk records.

---

### 7.2 Why Salesforce Has Limits

**Concept**
- Salesforce is a multi-tenant platform.
- Resources are shared among multiple organizations.
- Governor Limits prevent one transaction from consuming excessive resources.

**Key Learning**
- Governor Limits are engineering boundaries that encourage efficient software design.

---

### 7.3 The Limits You Must Respect

**Governor Limits Studied**

| Resource | Limit |
|----------|------:|
| SOQL Queries | 100 |
| Records Retrieved | 50,000 |
| DML Statements | 150 |
| DML Records | 10,000 |
| CPU Time | 10,000 ms |
| Heap Size | 6 MB |

**Key Learning**
- Understand resource usage instead of memorizing numbers.

---

### 7.4 One Innocent Loop

**Problem**

```apex
for(Application__c app : Trigger.new){
    Student__c student = [
        SELECT Id FROM Student__c
        WHERE Id = :app.Student__c
    ];
}
```

**Issue**
- SOQL executes once per iteration.
- Can exceed Governor Limits.

**Solution**
- Collect IDs first.
- Perform one SOQL query outside the loop.

---

### 7.5 From Record Thinking to Collection Thinking

**Record Thinking**

```
One Record
↓

Process
```

**Collection Thinking**

```
Many Records
↓

Collect
↓

Query Once
↓

Process
↓

Update Once
```

**Key Learning**
- Bulkification means writing code that works for 1, 10, or 200 records.

---

### 7.6 Why Lists, Sets and Maps Matter

#### List
- Stores multiple records.
- Used for bulk DML.

#### Set
- Stores unique values.
- Removes duplicate IDs.

#### Map
- Stores key-value pairs.
- Provides quick access to queried records.

**Key Learning**
- Collections improve performance and reduce database operations.

---

### 7.7 Bulk Processing Pattern

```
Receive Records
↓

Collect IDs
↓

Query Once
↓

Store in Map
↓

Process in Memory
↓

Collect Updates
↓

One DML
```

**Key Learning**
- Standard pattern for writing bulk-safe Apex.

---

### 7.8 DML Inside Loop

**Problem**

```apex
for(Application__c app : applications){
    update app;
}
```

**Issue**
- Executes one DML per record.
- May exceed DML Governor Limit.

**Solution**

```apex
List<Application__c> appsToUpdate = new List<Application__c>();

for(Application__c app : applications){
    appsToUpdate.add(app);
}

update appsToUpdate;
```

**Key Learning**
- Process records in memory.
- Perform one DML operation.

---

## Engineering Principles Learned

- Think at Scale
- Bulkification
- Collection Thinking
- Resource Optimization
- Query Once
- Update Once
- Avoid SOQL Inside Loops
- Avoid DML Inside Loops

---

## Sprint Outcome

After completing this sprint, I can:

- Explain Governor Limits.
- Write bulk-safe Apex.
- Use Lists, Sets, and Maps effectively.
- Avoid SOQL and DML inside loops.
- Design scalable Trigger logic.
- Follow the Bulk Processing Pattern.
