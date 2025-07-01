# Field-Level Security & CRUD Checks

Respecting a user’s permissions in Apex is critical to ensure application security.
Salesforce provides ways to enforce Create, Read, Update, and Delete (CRUD) access as well as field-level security (FLS).
This section explains when and how to apply these checks.

---

## 🔹 Why FLS & CRUD Matter

Without permission checks, Apex code can:
- Violate user access rights
- Cause runtime exceptions
- Expose sensitive data
- Fail security reviews or audits

> ✅ Always check FLS/CRUD when accessing SObject fields in Apex

---

## 🔹 CRUD Checks — Object-Level Access

Before creating, reading, updating, or deleting records, confirm the user has access.

**Example:**
```apex
if (Schema.sObjectType.Account.isCreateable()) {
    insert new Account(Name = 'Test');
}
```

> 📌 Check `isCreateable()`, `isUpdateable()`, `isDeletable()`, `isAccessible()`

---

## 🔹 FLS Checks — Field-Level Access

Before accessing or modifying a field, check if the current user has permission.

**Example:**
```apex
if (Schema.sObjectType.Account.fields.Name.isAccessible()) {
    String name = acc.Name;
}
```
```apex
if (Schema.sObjectType.Account.fields.Status__c.isUpdateable()) {
    acc.Status__c = 'Active';
}
```

---

## 🔹 When to Enforce

Enforce FLS and CRUD when your logic:
- Reads user-facing field values
- Writes/updates SObjects
- Performs record-level insert/delete

> ✅ Especially important in utility classes and trigger handlers

---

## 🔹 When You Can Skip

You may skip explicit checks in:
- Test classes (system context bypasses checks)
- Internal automation only (e.g., Queueable run as System)
- Platform features like Flows or UI that already enforce it declaratively

> ⚠️ Never skip checks in shared classes exposed to users or API

---

## 🔹 Helper Utility Approach (Recommended)

To keep code clean, use a helper class to encapsulate security logic.

**Example:**
```apex
public class SecurityUtil {
    public static Boolean canEditField(Schema.SObjectField fieldRef) {
        return fieldRef.getDescribe().isUpdateable();
    }
}
```
```apex
if (SecurityUtil.canEditField(Account.Name)) {
    acc.Name = 'New Name';
}
```

> ✅ Makes enforcement reusable and centralized

---

## 🔹 Related References

- [Constants.cls](./ConstantsClassUsage.md) – avoid hardcoding access-restricted field names
- [Deployment Checklist](./DeploymentChecklist.md) – confirms that FLS/CRUD are verified before deployment

---

✅ **Summary:**
- Always enforce object and field-level access where applicable
- Use `Schema.sObjectType` for safe access checks
- Consider helper classes for cleaner enforcement
- Don’t assume automation or UI covers everything

📌 [Back to Index](./README.md)

