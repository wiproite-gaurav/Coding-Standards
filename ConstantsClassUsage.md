# Using Constants.cls

The `Constants.cls` file is a shared Apex class that stores reusable, static constant values.
These constants represent IDs, labels, field values, object prefixes, and other config-level data that may change across environments.

This practice helps improve maintainability, reduce hardcoding, and centralize logic.

---

## 🔹 Why Use Constants?

Hardcoding values like Record Type IDs or Profile Names in Apex:
- Breaks portability across orgs
- Makes code harder to maintain
- Can lead to bugs during deployment

**✅ Use constants for:**
- Record Type IDs
- Profile IDs / Role Names
- Custom Setting keys
- Business Unit Codes
- Task Subjects
- Object Prefixes

---

## 🔹 Constants Class Structure

Place all constants in a dedicated `Constants.cls` Apex class.
Use ALL_CAPS with underscores for variable names.

**Example:**
```apex
public class Constants {
    public static final String SYSADMIN_USER_PROFILE_ID = '00e1x0000001234';
    public static final String TASK_TYPE_EMAIL = 'Email';
    public static final String BUO_RECORD_TYPE_ID = '0128x000000abcd';
}
```

> ✅ Keep the file organized using sections or groups if needed

---

## 🔹 Usage in Code

Use constants from `Constants.cls` instead of hardcoding values inline.

**Example:**
```apex
User testUser = SetupTestObjects.testUser(Constants.SYSADMIN_USER_PROFILE_ID, 'Test User');
```

---

## 🔹 Usage in Test Classes

Tests should also refer to shared constants to stay consistent.

**Example:**
```apex
@isTest
private class OpportunityServiceTest {
    @isTest
    static void testScenario() {
        User testUser = SetupTestObjects.testUser(Constants.SYSADMIN_USER_PROFILE_ID, 'Test Admin');
    }
}
```

> ✅ Ensures test logic matches deployed logic and avoids hardcoded test IDs

---

## 🔹 When to Use vs Define Locally

Use Constants.cls if:
- The value is reused across multiple classes or tests
- It's environment-dependent (e.g., ID or config)

Define locally in a class if:
- The value is truly unique to that class only
- It's unlikely to be reused elsewhere

---

## 🔹 Custom Settings Overview

Custom Settings are lists of custom data that your Salesforce org can access without triggering SOQL queries against your data limits.
They come in two types: **List** and **Hierarchy**.

### List Custom Settings
- Simple key-value pair structure
- Similar to custom objects but with special governor limit benefits
- Access via `.getInstance()` method without counting toward query limits

**Example:**
```apex
// Setup: Create custom setting "Integration_Config__c" with field "API_Endpoint__c"
Integration_Config__c config = Integration_Config__c.getInstance();
String endpoint = config.API_Endpoint__c;
```

### Hierarchy Custom Settings
- Allow you to associate specific rows with specific **Users** or **Profiles**
- When retrieving a value, Salesforce looks up in this order:
  1. Custom setting row for the currently running **User ID**
  2. Custom setting row for the current User's **Profile**
  3. Organization-wide **default row** (if defined)

**Use Cases:**
- User-specific defaults (e.g., preferred account, last selected record)
- Profile-based commission rates or approval levels
- Role-specific business rules

**Example:**
```apex
// Setup: Create Hierarchy Custom Setting "UserDefaults__c" with field "Default_Account__c"
UserDefaults__c userDefault = UserDefaults__c.getInstance();
// If current user has a specific record, it returns that; else their profile's default; else org default
Id defaultAccountId = userDefault.Default_Account__c;
```

**In Formulas:**
```
$Setup.UserDefaults__c.Default_Account__c
```

---

## 🔹 Governor Limit Benefits of Custom Settings

### Zero Query Impact
When using Custom Settings with the **`.getInstance()`** method (not SOQL), they:
- **Do NOT count** toward your SOQL query limit
- **Do NOT count** toward your row retrieval limit

**Example (No Governor Impact):**
```apex
// This does NOT count as a query
Integration_Config__c config = Integration_Config__c.getInstance();
String apiKey = config.API_Key__c;
```

### Important Caveats
- Other governor limits **still apply**:
  - Heap size limit
  - Number of script statements
  - Aggregate query limits (if using SOQL instead of getInstance())

- You must use **`.getInstance()`**, **NOT SOQL**, to get the benefit:

```apex
// ✅ Correct - No query limit impact
Integration_Config__c config = Integration_Config__c.getInstance();

// ❌ Wrong - Counts as a query
List<Integration_Config__c> configs = [SELECT ... FROM Integration_Config__c];
```

---

## 🔹 Custom Settings vs Custom Metadata

| Feature | Custom Settings | Custom Metadata |
|---------|-----------------|-----------------|
| **Lookup Fields** | No | Yes |
| **Governor Limit Impact** | No (with getInstance) | Yes (requires SOQL) |
| **Change Data Capture** | Yes | No |
| **Package Deployment** | Can be org-dependent | Packaged with deployment |
| **Use Case** | Runtime config, user-specific defaults | Reference data, validation rules, config |
| **Hierarchy Support** | Yes (List vs Hierarchy type) | No |

---

## 🔹 Custom Metadata Best Practices

Custom Metadata should be used for:
- **Validation Rules and Error Messages** (can reference in validation rules and flows)
- **Reference Data** (status codes, mappings, business rules)
- **Integration Configuration** (endpoint mappings, field mappings)
- **System Configuration** that needs to be **packaged and versioned**

**Naming Convention:** `<Purpose>_<Type>__mdt`

**Examples:**
```
Field_Validation_Rules__mdt
Error_Message_Config__mdt
Integration_Settings__mdt
```

**Using in Validation Rules:**
You can reference custom metadata in Salesforce validation rules without SOQL:
```
NOT(ISBLANK(Text_Field__c)) 
AND 
$CustomMetadata.Field_Validation_Rules__mdt.Require_Phone_if_Lead.Enabled__c
```

---

## 🔹 When to Use Each Option

**Use Constants.cls when:**
- Value is **developer-owned** and rarely changes
- Value is **org-independent** (same across all orgs)
- Value is **simple data** (strings, IDs, numbers)
- Value doesn't need admin-level control

**Use Custom Settings (List) when:**
- You need **runtime configuration** that admins can change
- You want **zero SOQL impact** for lookups
- Changing it **shouldn't require a deployment**

**Use Custom Settings (Hierarchy) when:**
- You need **user-specific or profile-specific** defaults
- Different users/profiles need **different rules**
- You want **automatic hierarchy resolution** (user → profile → org default)

**Use Custom Metadata when:**
- Data must be **packaged** with your deployment
- You need **lookup field relationships**
- Data should be **version controlled** with the code
- You need to reference it in **validation rules or flows**

---

## 🔹 Complement With Custom Metadata

Use `Constants.cls` for static, developer-owned values.  
For values that may change or need admin control, use Custom Settings or:
- [Custom Metadata vs Settings](./CustomMetadataVsSettings.md)

> ✅ Consider `Constants.cls`, Custom Settings, and Custom Metadata as complementary tools

---

## 🔹 Naming Conventions

- Use all caps: `FIELD_NAME_CONSTANT`
- Use descriptive names: `DEFAULT_TASK_TYPE`
- Group related constants logically (optionally using inner classes)

---

✅ **Summary:**
- Never hardcode IDs, types, or config keys directly in code
- Use a shared Constants class to centralize values
- Keep naming clear, and update values in one place
- Use metadata for admin-owned values

📌 [Back to Index](./README.md)

