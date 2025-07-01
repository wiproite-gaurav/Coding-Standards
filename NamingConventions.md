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
<ClassName>Test
```
**Examples:**
- `AccountTriggerHandlerTest`
- `OpportunityServiceTest`

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

✅ **Summary:**
- Use consistent and descriptive names
- Follow casing standards (camelCase, PascalCase, ALL_CAPS)
- Reflect purpose and structure
- Avoid abbreviations and vague terms

📌 [Back to Index](./README.md)
