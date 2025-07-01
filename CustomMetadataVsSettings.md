# Custom Metadata vs Custom Settings

Both Custom Metadata and Custom Settings allow storing configuration data that drives logic in Apex without needing hardcoded values.
However, they differ in purpose, behavior, and access patterns.
This section explains when to use which and how they improve flexibility in your code.

---

## 🔹 Custom Metadata

Custom Metadata is metadata — meaning it is **deployable**, versioned, and accessible in all environments.
It is recommended for most use cases where admins or devs need to manage logic via declarative config.

### ✅ Use when:
- Values need to be included in change sets or deployments
- Logic should vary by record or type (e.g., validator matrix, business rules)
- You need declarative, org-wide control

### 📌 Examples:
- Enable/disable feature flags
- Map record types to processors
- Define dynamic limits or rule sets

---

## 🔹 Custom Settings

Custom Settings store config data but are **data**, not metadata.
That means values are **org-specific**, **not deployable via metadata** unless using tools like data loader.

They can be:
- **List Settings** (global config)
- **Hierarchy Settings** (user/profile-based overrides)

### ✅ Use when:
- You need user-specific or profile-specific overrides
- Values differ by sandbox/prod
- Org-only configs not intended for deployment

### 📌 Examples:
- Enable integrations only for certain users
- Override thresholds per profile

---

## 🔹 When to Use Constants.cls Instead

Use `Constants.cls` (see [ConstantsClassUsage](./ConstantsClassUsage.md)) if:
- The value is static and doesn’t change per org/user
- You want to avoid querying config objects

> ✅ Constants are compile-time safe and fast — but require code deployment for changes

---

## 🔹 Access Patterns in Apex

**Custom Metadata Query:**
```apex
MyMetadata__mdt config = [SELECT Label, Value__c FROM MyMetadata__mdt WHERE DeveloperName = 'Example'];
```

**Custom Setting Query:**
```apex
MySetting__c setting = MySetting__c.getInstance();
String value = setting.Default_Value__c;
```

> 📌 Custom Metadata requires SOQL, while Custom Settings have special `.getInstance()` methods

---

## 🔹 Comparison Table

| Feature                        | Custom Metadata         | Custom Settings        |
|-------------------------------|-------------------------|------------------------|
| Deployable via Metadata       | ✅ Yes                  | ❌ No (unless manual)  |
| Supports Hierarchy            | ❌ No                   | ✅ Yes                 |
| Accessible via SOQL           | ✅ Yes                  | ✅ Somewhat            |
| Version Controlled            | ✅ Yes                  | ❌ No                  |
| Visible in Setup              | ✅ Yes                  | ✅ Yes                 |
| Ideal for                     | Business rules, switches | User overrides       |

---

✅ **Summary:**
- Use **Custom Metadata** for versioned, deployable, org-wide config
- Use **Custom Settings** for user/profile-specific runtime config
- Use **Constants.cls** for hardcoded static values
- Choose based on scope: environment, user, or static

📌 [Back to Index](./README.md)

