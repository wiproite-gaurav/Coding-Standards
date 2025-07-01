# Trigger Best Practices

Apex triggers are powerful — but if not structured properly, they can become difficult to manage and debug.  
This section outlines best practices for writing scalable, maintainable, and bulk-safe triggers.

---

## 🔹 One Trigger per Object

Always create **only one trigger** per object.

**Example:**
```apex
trigger AccountTrigger on Account (before insert, before update, after insert, after update) {
    AccountTriggerHandler.handle(Trigger.new, Trigger.oldMap);
}
```

> ✅ Keep all logic inside a separate handler class
> ❌ Avoid having multiple triggers for the same object with overlapping events

---

## 🔹 Use a Trigger Handler Class

Move all logic to a dedicated Apex class (commonly suffixed with `TriggerHandler`).
This keeps the trigger file clean and centralizes business logic.

**Example Handler Class:**
```apex
public class AccountTriggerHandler {
    public static void handle(List<Account> newList, Map<Id, Account> oldMap) {
        if (Trigger.isBefore && Trigger.isInsert) {
            handleBeforeInsert(newList);
        }
    }

    private static void handleBeforeInsert(List<Account> newList) {
        // Logic goes here
    }
}
```

> 📌 Separate methods by event type and context (before/after, insert/update/delete)

---

## 🔹 Use Context Variables Properly

Context variables like `Trigger.isInsert`, `Trigger.isBefore`, and `Trigger.isAfter` should be used to control the flow within the handler class.

**Good Practice:**
```apex
if (Trigger.isBefore && Trigger.isDelete) {
    handleBeforeDelete(Trigger.old);
}
```

> ✅ Improves readability and avoids accidental logic execution

---

## 🔹 Keep Trigger Logic Bulk-Safe

Always write trigger logic that can handle bulk operations (e.g., imports, mass updates).
Avoid writing logic that assumes a single record.

**Example:**
```apex
for (Account acc : Trigger.new) {
    if (acc.Name == null) {
        acc.Name = 'Default Name';
    }
}
```

> ❌ Avoid SOQL/DML inside `for` loops

---

## 🔹 Avoid Recursive Trigger Calls

Use static variables (like a `TriggerControl` class) to prevent recursive calls.

**Example:**
```apex
public class TriggerControl {
    public static Boolean isFirstRun = true;
}
```
```apex
if (TriggerControl.isFirstRun) {
    TriggerControl.isFirstRun = false;
    // perform logic
}
```

> ✅ Prevents infinite loops and duplicate logic execution

---

## 🔹 Avoid Business Logic in Trigger Directly

Triggers should act as **event routers** — all business logic should go to service or handler classes.
This makes the code more testable and easier to maintain.

> ❌ Avoid doing validations, updates, or external calls inside the trigger block itself.

---

## 🔹 Consistent Trigger Naming

Use the standard format:
```
<ObjectName>Trigger
```
**Examples:**
- `LeadTrigger`
- `CaseTrigger`
- `OpportunityTrigger`

> ✅ Makes code easy to navigate and search

---

✅ **Summary:**
- Use one trigger per object
- Route logic to a handler class
- Respect bulk-safe patterns and context variables
- Prevent recursion
- Never place business logic directly in trigger

📌 [Back to Index](./README.md)

