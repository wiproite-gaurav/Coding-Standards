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

## 🔹 Complement With Custom Metadata

Use `Constants.cls` for static, developer-owned values.  
For values that may change or need admin control, use:
- [Custom Metadata vs Settings](./CustomMetadataVsSettings.md)

> ✅ Consider `Constants.cls` and Custom Metadata as complementary

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

