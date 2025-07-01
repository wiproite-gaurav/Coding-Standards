# Error Handling & Logging

Proper error handling makes your code reliable, easier to debug, and safer in production.
This section covers how to handle exceptions, bubble them correctly, and log issues for observability.

---

## 🔹 Always Use Try-Catch Blocks

Wrap risky logic in try-catch blocks to prevent runtime exceptions from breaking the transaction.

**Example:**
```apex
try {
    update accountList;
} catch (DmlException ex) {
    System.debug('DML failed: ' + ex.getMessage());
}
```

> ✅ Never leave critical logic unprotected

---

## 🔹 Use Specific Exception Types

Catch specific exceptions like `DmlException`, `NullPointerException`, `QueryException`, etc. where possible.

> ✅ Improves clarity and makes debugging faster

---

## 🔹 Don’t Swallow Exceptions Silently

Avoid empty catch blocks or just printing the error to logs.
Always take meaningful action or rethrow the error if necessary.

**Bad:**
```apex
catch (Exception ex) {
    // ignore
}
```

**Better:**
```apex
catch (Exception ex) {
    Logger.logError('Process failed', ex);
    throw ex;
}
```

> ⚠️ Silent failures are dangerous and hard to detect

---

## 🔹 Use Custom Exception Classes

For complex flows, create your own exception types to improve readability.

**Example:**
```apex
public class ValidationException extends Exception {}

throw new ValidationException('Missing required fields');
```

> 📌 This is especially useful in service or validation layers

---

## 🔹 Log Errors Centrally

Use a logging utility or custom object to capture errors consistently.
Include:
- Timestamp
- User ID
- Method name
- Exception message

**Example Utility:**
```apex
public class Logger {
    public static void logError(String context, Exception ex) {
        System.debug('[' + context + '] ' + ex.getMessage());
        // Optionally write to a custom object
    }
}
```

> ✅ Consistent logging simplifies root cause analysis

---

## 🔹 Bubble Up Errors When Needed

Allow lower-level errors to propagate upward if the caller needs to know.
Use `throw` to re-raise caught exceptions.

**Example:**
```apex
catch (Exception ex) {
    Logger.logError('Handler failed', ex);
    throw ex;
}
```

> ✅ Useful in trigger handlers, batch jobs, or services

---

## 🔹 Related Topics

- [Deployment Checklist](./DeploymentChecklist.md) — validate error handling before moving to prod
- [Test Class Design](./TestClasses.md) — include negative test cases for exception handling

---

✅ **Summary:**
- Wrap risky code in try-catch
- Use specific and custom exceptions
- Always log or report errors
- Rethrow if the caller needs to act
- Don’t hide exceptions — handle them intentionally

📌 [Back to Index](./README.md)

