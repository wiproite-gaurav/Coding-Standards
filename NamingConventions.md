# Naming Conventions

Consistent naming is key to writing clear, maintainable, and scalable Salesforce code.
It helps your teammates (and future you) understand the intent of the code at a glance.
This section outlines naming standards for Apex classes, triggers, test classes, variables, constants, and helper utilities.

---

## 🔹 Apex Class Naming

**Format:**
```
<Object><Purpose>[Suffix]
```
**Examples:**
- `AccountTriggerHandler`
- `OpportunityService`
- `CaseValidator`

> 📌 Suffixes like `Handler`, `Service`, `Util`, `Test`, and `Constants` help define the intent of the class.

---

## 🔹 Trigger Naming

**Format:**
```
<ObjectName>Trigger
```
**Examples:**
- `AccountTrigger`
- `ContactTrigger`

> 🚫 Avoid trigger names like `MainTrigger`, `MyTrigger`, or abbreviations like `AccTrg`.

---

## 🔹 Test Class Naming

**Format:**
```
test<ClassName>
```
**Examples:**
- `testAccountTriggerHandler`
- `testOpportunityService`

> ✅ This creates a 1:1 mapping between production and test logic.
> 📌 Keep test classes in the same namespace/folder if possible.

---

## 🔹 Variable Naming

Use camelCase for local variables and parameters.
Make names descriptive but concise.

**Examples:**
- `accountList`
- `isDuplicateFound`
- `formattedDate`

> ❌ Avoid vague names like `x`, `temp`, `data`, `flag` unless contextually obvious.

---

## 🔹 Constant Naming

Use ALL_CAPS with underscores for constants in Apex classes or Constants.cls.

**Examples:**
- `MAX_RETRY_COUNT`
- `DEFAULT_BATCH_SIZE`
- `SYSADMIN_USER_PROFILE_ID`

> 📌 Keep constants in a `Constants.cls` file if used across classes.

---

## 🔹 Utility Class Naming

**Format:**
```
<CommonPurpose>Util
```
**Examples:**
- `StringUtil`
- `DateHelper`
- `ValidationUtil`

> ⚠️ Keep utility methods generic and reusable.

---

## 🔹 Method Naming

Use camelCase and action-oriented names.

**Examples:**
- `calculateTotalAmount()`
- `fetchActiveContacts()`
- `validateRecordOwnership()`

> ✅ Method names should clearly state what they do.

---

## 🔹 Enum Naming

Use PascalCase for enum names and ALL_CAPS for enum values.

**Examples:**
```apex
enum RecordStatus {
    NEW,
    APPROVED,
    REJECTED
}
```

---

## 🔹 Folder Structure Naming (Optional)

If your org follows a layered folder structure:
- `triggers/` → for trigger files
- `handlers/` → trigger handler classes
- `services/` → service-level logic
- `utils/` → helper methods
- `tests/` → test classes

---

## 🔹 Field and Object Label Naming

Be as descriptive as possible and capitalize the 1st letter of every word with spaces between words.
Salesforce automatically converts labels to API names by adding underscores.

**Examples:**
- Label: `Success Based Capital` → API: `Success_Based_Capital__c`
- Label: `Vehicle Asset` → API: `Vehicle_Asset__c`

> 📌 Avoid acronyms. If acronyms are necessary, include the full name in the Help Text.
> ✅ For new fields and objects, keep the API name the same format as the Field Label.

---

## 🔹 Validation Rule Naming

Start with an **action verb** to clearly describe what the rule enforces.

**Format:**
```
<Action> <Object/Field> [<Condition>]
```

**Examples:**
- `Require_Capital_Project`
- `Restrict_Escalation_Status_Changes`
- `Validate_Stage_to_6_Pipeline`

> ✅ Makes it immediately clear what the rule does when viewing the object definition.

---

## 🔹 Approval Process Naming

Use **descriptive names** without dates. Format: `<ObjectAPIName>_<Purpose>`

**Examples:**
- `Account_Budget_Approval`
- `Opportunity_Executive_Review`
- `Contract_Legal_Review`

> 📌 Use metadata names, not UI-facing labels.

---

## 🔹 Master/Child Object Naming

Child objects should start with the **parent object name**, followed by a descriptor.

**Examples:**
- Parent: `Time_Sheet__c` → Child: `Time_Sheet_Item__c`
- Parent: `Splice_Point__c` → Child: `Splice_Point_Activity__c`

> ✅ This naming convention immediately shows the parent-child relationship.

---

## 🔹 Flow Naming

### Before-Save Flows
**Format:**
```
<ObjectAPIName>_BeforeSave
```

**Examples:**
- `Account_BeforeSave`
- `Opportunity_BeforeSave`

> ✅ These trigger before the record is saved to the database.

---

## 🔹 Process Builder and Automation Naming

Process Builders should follow one of 4 standard naming patterns per object based on their function:

### Handler Process
**Format:**
```
<ObjectAPIName>_handler
```
- Top-level process that executes when a record is inserted or updated
- Most similar to an Apex trigger
- **Example:** `Account_handler`

### Stager Process
**Format:**
```
<ObjectAPIName>_stager
```
- Specialized update handler revolving around a major stage transition
- Used for status or stage changes (e.g., Order Stage)
- **Example:** `Opportunity_stager`

### Headless Process
**Format:**
```
<ObjectAPIName>_headless
```
- Processes invoked only from other processes
- Used for creating reusable sub-processes
- **Example:** `Account_headless`

### Rules Process
**Format:**
```
<ObjectAPIName>_rules
```
- Creating activities/notifications that don't fit other categories
- **Example:** `Case_rules`

---

## 🔹 Lightning Component and Page Naming

### Lightning Record Pages
**Format:**
```
<Object Name> Record Page - <App Name>
```

**Examples:**
- `Account Record Page - Sales`
- `Opportunity Record Page - Revenue Operations`

### Reusable Lightning Components
**Prefix reusable components with `utility`.** Format: `utility<ComponentName>`

**Examples:**
- `utilityRecordSearch` → Component for querying and selecting specific records
- `utilityDatePicker`
- `utilityConfirmationModal`

> ✅ Makes it immediately clear which components are reusable vs object-specific.

---

## 🔹 Custom Metadata Naming

Name custom metadata similar to custom objects, using descriptive labels.

**Format:**
```
<Purpose>_<Type>__mdt
```

**Examples:**
- `Field_Validation_Rules__mdt`
- `Error_Message_Config__mdt`
- `Integration_Settings__mdt`

> 📌 Custom metadata is used for org-level configuration and is more flexible than custom settings.

---

✅ **Summary:**
- Use consistent and descriptive names
- Follow casing standards (camelCase, PascalCase, ALL_CAPS)
- Reflect purpose and structure
- Avoid abbreviations and vague terms

📌 [Back to Index](./README.md)
