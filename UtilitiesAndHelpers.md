# Reusable Utilities & Helper Classes

Utility classes promote code reuse, reduce duplication, and encapsulate logic that appears across triggers, services, and components.
This section outlines patterns and examples for writing clean, maintainable helper classes in Apex.

---

## 🔹 What Belongs in a Utility Class?

Utility classes should contain logic that is:
- Reused in multiple classes or flows
- General-purpose (not tied to one object)
- Stateless and side-effect free when possible

### ✅ Examples:
- String formatting
- Null checks or defaulting logic
- JSON parsing
- Exception formatting
- Security or permission checks

---

## 🔹 Naming Convention

Use a consistent suffix like `Util`, `Helper`, or `Tools`.

**Examples:**
- `StringUtil`
- `DateHelper`
- `SecurityUtil`

> ✅ Naming should clearly convey purpose and scope

---

## 🔹 Common Utility Patterns

**String Utility:**
```apex
public class StringUtil {
    public static Boolean isEmpty(String str) {
        return str == null || str.trim() == '';
    }
}
```

**Date Helper:**
```apex
public class DateHelper {
    public static Integer daysBetween(Date d1, Date d2) {
        return d2.daysBetween(d1);
    }
}
```

**Validation Utility:**
```apex
public class ValidationUtil {
    public static void assertNotNull(Object val, String fieldName) {
        if (val == null) {
            throw new ValidationException(fieldName + ' is required');
        }
    }
}
```

---

## 🔹 Error Logging Utility

For structured logging:
```apex
public class Logger {
    public static void logError(String label, Exception ex) {
        System.debug(label + ': ' + ex.getMessage());
        // optionally write to a custom log object
    }
}
```

> ✅ Reuse for consistent error tracking across codebase

---

## 🔹 Static Helper Methods in Test Classes

Use internal helpers for test data setup or assertions.

**Example:**
```apex
@isTest
private class AccountTriggerHandlerTest {
    private static Account createTestAccount() {
        return new Account(Name = 'Test');
    }
}
```

---

## 🔹 Best Practices

- Avoid placing business logic in utility classes
- Keep utilities focused and single-purpose
- Use `static` methods for stateless access
- Document methods if shared across team

---

## 🔹 Related Sections

- [Test Class Design](./TestClasses.md) — reuse test setup helpers
- [Error Handling & Logging](./ErrorHandlingAndLogging.md) — centralize logging

---

✅ **Summary:**
- Use utility classes to encapsulate repeatable logic
- Keep them focused and reusable
- Use consistent naming and static methods
- Leverage in both main code and test setups

📌 [Back to Index](./README.md)
