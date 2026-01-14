# Objects and Fields Best Practices

Building a strong data model is foundational to Salesforce success.
This guide covers object and field design, consolidation strategies, field types, and how to properly document your data structures.

---

## 🔹 General Guidelines

### Review Before Creating

Before creating a new object or field, **always review existing options** to see if something suitable already exists.
Consider future uses for this object or field when naming it.

> ✅ Consolidating overlapping structures saves technical debt and simplifies the data model.

### Documentation Requirements

**For Objects:**
- Add **help text** describing what the object does from the business perspective
- Include clear descriptions for admins/developers

**For Fields:**
- **Help Text**: Information useful to end users
  - The purpose of the field
  - How the field should be filled in
  - Who to contact with questions (Department or Role)
- **Description**: Information for developers/admins
  - Why the field was created
  - Who requested it
  - The end goal of collecting this data

### Planning for Growth

- **Consider adding Record Types on EVERY object**
  - It's much easier to refactor objects in the future if you start with record types from the beginning
  - Record types enable different page layouts, business rules, and processes per type

---

## 🔹 Object Design Decisions

### When to Create a New Object vs Adding a Field

When deciding to add fields to an existing object or create a new custom object, consider:

#### 1. **Consolidate Objects and Relationships**
- Group related data together, especially with common relationships to other objects
- Record types become important here—a common object can be bifurcated using record types, page layouts, and conditional logic
- The more generic the solution, the more flexible it becomes
- ⚠️ **Lookup Field Limit:** Max 45 lookup fields per object

#### 2. **Field Grouping by Purpose**
Look for fields that can be grouped together to form a discrete object based on **purpose, function, or process**.

**Example: Service Order Product Pricing**

**❌ Bad Approach:** Create separate fields on Service_Order__c
```
Service_Order__c.Fiber_MRR__c
Service_Order__c.Ethernet_MRR__c
Service_Order__c.Waves_MRR__c
Service_Order__c.DIA_MRR__c
Service_Order__c.Colo_MRR__c
```
Problems: Every reorganization or new product requires a new field and automation update.

**✅ Better Approach:** Create a child object
```
Service_Order_Price__c.Service_Order__c  <Lookup(Service_Order__c)>
Service_Order_Price__c.Product__c        <Picklist (Fiber, Ethernet, etc)>
Service_Order_Price__c.MRR__c            <Currency>
```
Benefit: Adding a product or reorganization only requires a picklist update.

#### 3. **Field Count on Existing Objects**
If an existing object already has many fields, it may be a candidate for creating a **discrete child object** by grouping related fields.

#### 4. **Technical or Governance Isolation**
Sometimes it makes sense or is necessary to isolate an entity for:
- Technical reasons (performance, limits)
- Governance (permissions, auditing)
- Change management (separate deployments)

#### 5. **Standard vs Custom Objects**
Ask: "Why do you need the standard object functionality?" You'll have to build it yourself if you go the custom object route.
- With custom objects, you lose current and future Salesforce features for that object type
- Standard objects get enhancements and features over time

### Determine Access Levels Up Front

- Which groups should be able to **view** the object?
- Which groups should be able to **edit** the object?
- **Identify Personally Identifying Information (PII)**
  - If this object or field relates to a person (internal or external)
  - PII is subject to restrictions under international law
  - Contact your Privacy Officer to determine appropriate access controls

---

## 🔹 Relationship Field Types

### Lookup Relationships

**Use when:**
- The parent record is **optional**
- Row-level sharing may vary between parent and child
- The child can exist independently

**Behavior:**
- Child can exist without parent
- No automatic deletion cascade
- Sharing is independent between records

### Master-Detail Relationships

**Use when:**
- The child **should not exist without the parent**
- You need **Rollup Summaries** on the parent
- Row-level sharing needs to be **enforced from parent to children**

**Behavior:**
- Deleting the parent deletes all children
- Child inherits parent's sharing/access
- Enables Rollup Summary fields

---

## 🔹 Field Type Best Practices

### Formula Fields
- **Use before leveraging triggers** to copy data around
- Can aggregate data from related objects
- ⚠️ Limit: 10 unique relationships across cross-object merge fields

**Example:**
```
Account.Billing_Country = Contact.Mailing_Country
```

### Rollup Summary Fields
- **Requires a Master-Detail Relationship**
- Use to perform math functions (COUNT, SUM, AVG, MAX, MIN)
- **Automatically converts currency** when rolling up across multiple currencies
- Recalculates automatically when child records change

**When to Use:**
- Summarizing child record data on parent (total revenue, record count, etc.)
- When currency conversion is needed across records
- Avoid triggers just to calculate totals

### Checkboxes
- **Primarily for system automations**, not user-entered booleans
- Problem: A `false` value is impossible to distinguish from "not set"
- **Better Alternative:** Use a Yes/No picklist with a default value of null

### Text Fields
- **Keep the length to what is actually needed**
- This improves UI layout and performance

### Long Text Area Fields
- **Limit to the character count actually needed**
- ⚠️ Avoid the full 131,000 character limit due to storage and layout restrictions
- 131,000 characters are rarely necessary (that's longer than H.P. Lovecraft's "The Call of Cthulhu"!)

**Note on Text Area Fields:** These can become dumping grounds for unactionable information.
- If important data needs to be captured consistently, use **picklists or dependent picklists** instead
- Picklists are consistently actionable and reportable
- Freeform text fields are neither

### Currency Fields

**Important Considerations:**

- **All currency fields on a record are in the same currency**
- Salesforce supports multiple currencies with exchange rates set per quarter
- **Rollup Summaries automatically convert currency** to the parent record's currency
- **Apex code does NOT automatically convert** — use the `CurrencyUtil` class for conversions
- When exchange rates update, converted amounts update immediately (except Apex-calculated summaries)

**Example:** If a parent record is in USD and you rollup children in CAD, EUR, etc., the Rollup Summary automatically converts all to USD before summing.

### DateTime vs Date + Time Fields

- **DateTime Field:**
  - Stored in UTC
  - Can result in **timezone drift** when viewed across different timezones
  - Better for recording exact moments (e.g., record creation)

- **Date Field + Time Field:**
  - Better for **scheduling purposes** where local time matters
  - Requires help text indicating the timezone context
  - User must select the correct time for the timezone work will be done in

**Example:** For a field like "Scheduled Work Start Time", use Date + Time fields with clear timezone documentation.

### Geolocation Fields

- Currently not heavily used at most organizations
- Different format than standard numbers
- Comes with added Salesforce features (distance calculations, mapping)
- **Consult with architecture** before implementing

### Number Fields

- **Confirm with users** the acceptable ranges
- **Define decimal places needed**
- Helps prevent invalid data entry

### Picklists and Dependent Picklists

- **Preferred over free-form text** when capturing standardized data
- Enable validation, reporting, and automation
- Can be dependent on other picklist values (e.g., Country → State)

---

## 🔹 Field History Tracking

### When to Enable Field History

Enable field history when it's valuable to know:
- **Who** changed the value
- **When** the value changed
- **What** the previous value was

⚠️ **Limit:** Maximum 25 fields per object can be tracked

### Auto-Posting to Feed

- Field history can be automatically posted to the object's Chatter feed
- Useful for audit trails and transparency
- Consider performance impact on high-volume objects

---

## 🔹 Avoiding Common Data Model Mistakes

### Don't Create Redundant Fields

If a field value can be calculated or looked up from another object, **don't store it as a separate field**.
- Use formula fields or rollup summaries instead
- Reduces maintenance burden and data inconsistency

### Don't Overuse Text Areas for Structured Data

Avoid dumping all data into notes fields. Instead:
- Break data into structured fields (picklists, lookups, etc.)
- Enables reporting, sorting, and filtering
- Improves data quality

### Don't Ignore Non-Required Fields

If a field isn't required for **any profile, permission set, or work group**, question whether you need it.
- Clutter reduces usability and system performance
- Re-examine the actual business need

### Don't Forget PII Considerations

Before creating a field that stores personal information:
- Identify that it's PII (relates to a person)
- Work with your Privacy Officer on access restrictions
- Ensure proper security and data handling

---

✅ **Summary:**
- Review existing objects/fields before creating new ones
- Use record types from the start for flexibility
- Document all objects and fields properly
- Choose the right field type (Formula, Rollup, Lookup, Master-Detail, etc.)
- Keep text fields to reasonable lengths
- Use picklists for standardized data, not free-form text
- Consider consolidation strategies to reduce clutter
- Plan for currency handling if needed
- Identify and protect PII appropriately

📌 [Back to Index](./README.md)
