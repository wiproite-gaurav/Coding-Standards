# Automation and Execution Order

Salesforce provides multiple tools for automating business logic. Choosing the right tool depends on **when** the automation needs to run and **what** it needs to do.
This guide explains each automation option and how they fit into the Salesforce Order of Execution.

---

## 🔹 When Does Automation Need to Run?

Before selecting an automation tool, determine:

### Synchronous Execution
**Timing:** In the transaction (during the save operation)

**Definition:** The transaction is the time between when the user (or other activity) initiates a system change and the database changes are committed.
From the user's perspective: **When they click save and when the page refreshes.**

**Use When:**
- The result needs to appear on the screen when the page refreshes
- Data must be updated in the database to maintain data integrity (e.g., creating a child record needs to update a parent summary)
- The logic is necessary for the record's validity

**Examples:**
- Field updates on the current record
- Creating related child records that need parent updates
- Validating dependent field relationships

### Asynchronous Execution
**Timing:** Soon after the transaction, in a separate process

**Use When:**
- The result is not needed in the current transaction
- Work doesn't affect the current record's validity
- The operation might take time

**Examples:**
- Sending email notifications
- Creating tasks or Chatter posts
- Calling external APIs
- Bulk record updates across related objects

### Scheduled Execution
**Timing:** At a specific time or on a recurring schedule

**Use When:**
- Work is mainly for reporting or aggregation
- Data needs to be collected regularly (e.g., nightly, weekly)

**Examples:**
- Updating Account totals/metrics
- Running batch cleanup operations
- Generating report data

---

## 🔹 Salesforce Order of Execution

When a record is saved, **the order matters**. Automation tools execute in a specific sequence:

1. **Validation Rules** (before database commit)
2. **Before-Save Flows** (Triggered Flows)
3. **Before Triggers** (Apex)
4. **System Validation** (Salesforce internal validation)
5. **Database Commit** ← Data is now saved
6. **Assignment Rules** (if applicable)
7. **Auto-Response Rules** (if applicable)
8. **Workflow Rules** (if enabled)
9. **Process Builder** (if enabled)
10. **After Triggers** (Apex)
11. **After-Update Flows** (Triggered Flows)
12. **Escalation Rules** (if applicable)

> 📌 **Key Insight:** Everything in steps 1-4 happens **before** the database commit. Anything in steps 6+ happens **after** the database commit.

---

## 🔹 Available Automation Tools

### Before-Save Flows (Triggered Flows)

**Execution Order:** Step 2 (before database commit)

**Use When:**
- You need **synchronous** updates to the current record before it's saved
- You want something **even faster than Apex triggers**
- You don't need to know what prior field values were
- You want a **declarative** (point-and-click) solution

**Key Characteristics:**
- Runs **before** the record is committed to the database
- Can update fields on the **current record only**
- No access to old values (can't compare before/after)
- Great for quick field updates based on conditions

**Naming Convention:** `<ObjectAPIName>_BeforeSave`

**Examples:**
```
Account_BeforeSave
Opportunity_BeforeSave
```

**Example Use Case:**
"When the status changes to 'Assigned', set the assigned date to today"

**Limitations:**
- Cannot create related records
- Cannot query data (no resource limits)
- Cannot call Apex

---

### Apex Triggers

**Execution Order:** Steps 3 (before) and 10 (after)

**Use When:**
- You need the **most flexibility** and control
- You need to know what values **changed** (before/after comparison)
- You need to **create or update multiple related records**
- You need to **query data** as part of the logic
- You need **complex conditional logic**

**Key Characteristics:**
- Most powerful automation tool
- Access to `Trigger.old` and `Trigger.new` (before/after values)
- Can query, create, update, delete records
- Subject to governor limits
- Requires code (Apex)

**Before Triggers:**
- Can modify the current record before it's saved
- Can prevent saves (addError)

**After Triggers:**
- Cannot modify the current record
- Used to create/update related records
- Used for audit logging

**Best Practice:** Use a **single trigger per object** with a **TriggerHandler class** for modularity.

**Naming Convention:** `<ObjectName>Trigger`

**Example:**
```apex
trigger AccountTrigger on Account (before insert, before update, after insert, after update) {
    AccountTriggerHandler.handle(Trigger.new, Trigger.old, Trigger.isInsert, Trigger.isUpdate);
}
```

---

### Workflow Rules (Deprecated)

⚠️ **Note:** Workflow Rules are **deprecated** and being replaced by Flows and Process Builder. Avoid creating new ones.

---

### Process Builder

**Execution Order:** Step 9 (after database commit)

**Use When:**
- You need **asynchronous** logic after the record is saved
- You want to create or update **related records**
- You want a **declarative** solution for complex workflows
- You need to send notifications or create tasks

**Key Characteristics:**
- Runs **after** the record is saved
- Great for multi-step workflows
- Can invoke Apex actions
- Can create/update records across objects
- Can send notifications

**Standard Naming by Function:**

| Pattern | Purpose | Example |
|---------|---------|---------|
| `<Object>_handler` | Top-level process for insert/update | `Account_handler` |
| `<Object>_stager` | Major stage transition logic | `Opportunity_stager` |
| `<Object>_headless` | Reusable sub-processes | `Account_headless` |
| `<Object>_rules` | Activities/notifications | `Case_rules` |

---

### After-Update Flows (Triggered Flows)

**Execution Order:** Step 11 (after database commit)

**Use When:**
- You need post-save logic
- You want a **low-code/no-code** solution
- You need to create related records asynchronously

**Key Characteristics:**
- Runs **after** the record is committed
- Can access final field values
- Can create, update, delete records
- More modern alternative to Process Builder

---

## 🔹 Comparison Table

| Tool | Sync | Timing | Declarative | Query Data | Create Records | Modify Current | Complexity |
|------|------|--------|-------------|-----------|-----------------|--|
| **Before-Save Flow** | ✅ | Pre-save | ✅ | ❌ | ❌ | ✅ | Low |
| **Before Trigger** | ✅ | Pre-save | ❌ | ✅ | ❌ | ✅ | High |
| **After Trigger** | ❌ | Post-save | ❌ | ✅ | ✅ | ❌ | High |
| **Process Builder** | ❌ | Post-save | ✅ | ❌ | ✅ | ❌ | Medium |
| **After-Update Flow** | ❌ | Post-save | ✅ | ❌ | ✅ | ❌ | Low |

---

## 🔹 Decision Tree

**Does the logic need to run before the record is saved?**

- **YES** → Use Before-Save Flow or Before Trigger
  - Is it simple field updates? → Use Before-Save Flow
  - Is it complex with queries/multiple records? → Use Before Trigger

- **NO** → Does it need to happen synchronously?
  - **YES** → Use After Trigger (must happen in same transaction)
  - **NO** → Use Process Builder or After-Update Flow
    - Is it a major workflow step? → Use Process Builder
    - Is it simple post-save logic? → Use After-Update Flow

---

## 🔹 Best Practices

### General Guidelines
1. **Minimize synchronous work** — too much blocking logic slows down the UI
2. **Use the simplest tool for the job** — declarative first, code only when needed
3. **Avoid recursive logic** — use flags to prevent infinite loops
4. **Document why** — leave comments explaining business logic
5. **Test edge cases** — bulk operations, null values, permission changes

### Apex Trigger Guidelines
1. **One trigger per object** with a handler class
2. **Use collections** (Set, List, Map) for bulk-safe code
3. **Query outside the loop** to avoid hitting limits
4. **Use context variables** (`Trigger.isInsert`, `Trigger.isUpdate`, etc.)
5. **Handle exceptions gracefully** with proper error messages

### Flow Guidelines
1. **Keep flows focused** — one responsibility per flow
2. **Use fault paths** — handle errors and edge cases
3. **Test with different data** — bulk, empty, invalid values
4. **Avoid complex nested logic** — break into sub-flows if needed
5. **Monitor flow execution** — check for errors and performance issues

---

✅ **Summary:**
- Understand **Salesforce Order of Execution**
- Choose the right tool based on timing (sync vs async) and complexity
- Use Before-Save Flows for simple pre-save updates
- Use Apex Triggers for complex logic with queries
- Use Process Builder or After-Update Flows for post-save workflows
- Keep automation modular, documented, and tested
- Avoid hardcoding; use Constants and Custom Metadata

📌 **Resources:**
- [Salesforce Order of Execution](https://help.salesforce.com/s/articleView?id=sf.apex_triggers_order_of_execution.htm)
- [Before-Save Flows Best Practices](https://help.salesforce.com/s/articleView?id=sf.flow_howto_before_save.htm)
- [Apex Trigger Best Practices](./TriggerBestPractices.md)

📌 [Back to Index](./README.md)
