# Design Approach

Before building any Salesforce solution, it's critical to step back and think about the **most effective and efficient way** of implementing it.
This guide covers principles for designing solutions that solve real business problems without unnecessary complexity.

---

## 🔹 Core Design Principles

### Start With the Business Problem

**Always ask:** "What problem are we solving?"

- Don't jump straight to building objects and fields
- Understand the business process and workflow
- Identify the actual user needs (not just the requested solution)
- Document the outcome you're trying to achieve

**Example:**
- ❌ Bad: "We need to add an Account field to track quarterly revenue"
- ✅ Good: "We need to track and forecast quarterly revenue by product line to improve sales planning"

### Consider Reporting Before Building Fields

**Critical Principle:** Before adding a new field or object, ask: **"Can we solve this with a report?"**

Many organizations add custom fields when a **report or dashboard** would be a better, simpler solution.

**Examples:**

**Scenario 1: Account Historical Analysis**
- ❌ Don't: Create 12 fields for monthly revenue (`Jan_Revenue__c`, `Feb_Revenue__c`, etc.)
- ✅ Do: Create a child object `Account_Monthly_Revenue__c` with lookup to Account
- ✅ Better: Build a report grouping Opportunities by month and year

**Scenario 2: Contact Engagement Tracking**
- ❌ Don't: Add fields like `Last_Email_Open_Date__c`, `Email_Click_Count__c`
- ✅ Do: Use an Email Tracking tool's dashboard or build a report from Activity records
- ✅ Better: Let the email platform provide the analytics; focus on the sync data

**Scenario 3: Order Fulfillment Status**
- ❌ Don't: Manually update an `Order_Status__c` field
- ✅ Do: Create a formula or rollup summary based on related records
- ✅ Better: Use a workflow or Process Builder to auto-update based on child record changes

**Benefits of Reports Over Fields:**
- No data bloat (smaller org)
- Less maintenance (change the report, not the data model)
- More flexibility (combine data in different ways)
- Better performance (don't query every record)

---

## 🔹 Work Collaboratively With Users

### Involve Business Stakeholders

Before finalizing design:
1. **Meet with the end users** who will use the system daily
2. **Understand their workflows** — don't assume you know
3. **Ask about edge cases** — "What about when...?"
4. **Validate assumptions** — "Would this solve your problem?"
5. **Get sign-off** — confirm stakeholders agree with the design

### Document Requirements

**Create a simple requirements document that includes:**
- Business problem statement
- Key user workflows
- Data inputs and outputs
- Performance expectations
- Integration needs

**Example:**
```
Problem: Sales team needs to track which leads are marketing-qualified 
vs sales-qualified and prioritize follow-up.

User Workflow:
1. Lead is created by marketing system
2. Lead is automatically scored (marketing automation)
3. Sales user reviews lead and decides if it's qualified
4. If qualified, it's assigned to a sales rep
5. If not qualified, it's marked as "nurture" for future follow-up

Data Needs:
- Lead score (auto-calculated from marketing system)
- Qualification status (Sales user enters)
- Assigned sales rep (based on round-robin)
```

---

## 🔹 Effective Solution Design

### Prefer Declarative Solutions

**Order of preference:**
1. **Configuration** (Reports, Dashboards, Page Layouts) — Easiest to change
2. **Declarative Automation** (Flows, Validation Rules, Process Builder)
3. **Custom Code** (Apex) — Most powerful but hardest to maintain

✅ Use code when declarative solutions can't solve the problem.
❌ Don't write code "just because" when a simpler solution exists.

### Think About Scalability From Day One

**Ask:**
- How will this solution perform with 10,000 records?
- What happens if a batch process affects 50,000 records at once?
- Can users wait 5 minutes for a process to complete?
- Do we need to paginate data or load it all?

**Apply principles from:**
- [Bulk-Safe Code](./BulkSafeCode.md)
- [Governor Limits](./GovernorLimits.md)

### Plan for Change

**Design with flexibility in mind:**
- Use Record Types from the start (easier to add variations later)
- Avoid hardcoding business logic (use Constants, Custom Metadata)
- Group related fields into child objects (easier to add new product types)
- Document decisions (why did you choose this approach?)

**Example:**
If you think you might add new products/services in the future, use a flexible structure from the start:
```
✅ Service_Order_Price__c (child object)
   - Product_Type__c (picklist: Fiber, Ethernet, Waves, DIA, Colo)
   - Price__c
   
vs

❌ Service_Order__c (parent object)
   - Fiber_Price__c
   - Ethernet_Price__c
   - Waves_Price__c
   - (need new field for each new product)
```

### Understand Limits and Trade-Offs

Every solution has trade-offs:
- **Real-time data**: Requires more frequent syncs (more API calls)
- **Complex validation**: Slows down saves (needs to run quickly)
- **Audit trail**: Increases storage (can hit org limits)
- **Custom UI**: Better UX but requires maintenance

**Document trade-offs with stakeholders:**
"We can get you real-time data, but it means syncing every 5 minutes, which costs X API calls/month"

---

## 🔹 Design Review Checklist

Before building, review:

- [ ] **Business Problem Clarity** — Stakeholders and team agree on the problem
- [ ] **Solution Fit** — Is a report/dashboard enough? Or do we need data model changes?
- [ ] **User Involvement** — Have end users been consulted?
- [ ] **Scalability** — Will it work with 10X the current data volume?
- [ ] **Declarative First** — Is this using the simplest approach available?
- [ ] **Data Model** — Is the object/field structure efficient and flexible?
- [ ] **Performance** — Are there governor limit concerns?
- [ ] **Integration** — How does this connect to other systems?
- [ ] **Security** — Are permissions and data access considered (PII, FLS, CRUD)?
- [ ] **Testability** — Can this solution be tested thoroughly?
- [ ] **Maintenance** — Who will maintain this after launch?
- [ ] **Documentation** — Are design decisions documented?

---

## 🔹 Common Design Anti-Patterns to Avoid

### ❌ "Uber Object" Anti-Pattern
**Problem:** One massive object with 200+ fields

**Solution:**
- Evaluate whether fields can be grouped into child objects
- Consider consolidating fields that relate to the same concept
- Use record types to handle variations

### ❌ Hardcoding Business Logic
**Problem:** Constants, values, and logic embedded directly in Apex code

**Solution:**
- Use [Constants.cls](./ConstantsClassUsage.md) for static values
- Use [Custom Metadata](./CustomMetadataVsSettings.md) for configurable rules
- Keep business rules in metadata, not code

### ❌ "Just Add a Field" Approach
**Problem:** Every new requirement = new field

**Solution:**
- First: Can this be solved with a report?
- Second: Can this be a lookup or child object?
- Last: Add a field if truly necessary

### ❌ Ignoring User Workflows
**Problem:** Building what you think users need, not what they actually need

**Solution:**
- Always involve end users in design
- Observe how they work (shadowing)
- Validate the solution improves their workflow

### ❌ No Planning for Edge Cases
**Problem:** Solution works for 90% of cases, breaks on edge cases

**Solution:**
- Ask "What could go wrong?"
- Build in validation and error handling
- Test with messy, real data

---

## 🔹 Design Documentation Template

```
## Solution Design: [Feature Name]

### Problem Statement
[What business problem are we solving?]

### User Workflow
1. [Step 1]
2. [Step 2]
...

### Proposed Solution
[High-level description of the solution]

### Data Model Changes
- New Objects: [List]
- New Fields: [List with field types]
- Relationships: [List]

### Automation Required
- [Process Builder/Flow/Trigger] — [What it does]

### Reporting & Analytics
- [Reports/Dashboards] — [What they show]

### Performance Considerations
- Estimated record volume: [X records]
- Expected query load: [X records retrieved]
- Governor limit concerns: [List any]

### Security & Access
- Who can see this data: [Profiles/Permission Sets]
- PII Considerations: [Yes/No, describe if yes]

### Testing Strategy
- Positive cases: [Test scenarios]
- Negative cases: [Error scenarios]
- Bulk scenarios: [High-volume testing]

### Implementation Timeline
- Phase 1: [What's in scope]
- Phase 2: [Future enhancements]

### Success Metrics
- How will we know this solution works?
```

---

✅ **Summary:**
- Start with the **business problem**, not the technical solution
- **Consult with users** before building
- Consider **reports and dashboards** before adding fields
- Choose **simple, declarative solutions** first
- Design for **scalability and change**
- Document **design decisions and trade-offs**
- Avoid building on assumptions — validate with real users

📌 [Back to Index](./README.md)
