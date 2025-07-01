# Deployment Readiness Checklist

Before deploying any new code to a higher environment, it's important to validate that your changes follow best practices and won't cause issues in production.  
This checklist includes the key points you should review before every deployment.

---

## 🔹 Naming Conventions Are Followed

Ensure all class, trigger, method, variable, and test class names follow the [Naming Conventions](./NamingConventions.md).

> ✅ Consistency improves readability and maintainability.

---

## 🔹 No Hardcoded IDs or Strings

Avoid hardcoding record type IDs, profile names, or other environment-specific values.  
Use:
- [Constants.cls](./ConstantsClassUsage.md)
- [Custom Metadata vs Settings](./CustomMetadataVsSettings.md)

> ✅ This helps ensure portability between environments.

---

## 🔹 FLS and CRUD Are Enforced

Apex code must respect user permissions.  
Ensure CRUD and FLS checks are performed before accessing or modifying data.

See: [Field-Level Security](./FieldLevelSecurity.md)

> 🔒 Important for security compliance and preventing runtime errors.

---

## 🔹 Code is Bulk-Safe and Limit-Aware

Revisit the [Bulkification](./BulkSafeCode.md) and [Governor Limits](./GovernorLimits.md) sections.  
Check that:
- No SOQL or DML inside loops
- CPU and heap usage are within limits

> ✅ Your code should work the same with 1 record or 200.

---

## 🔹 Error Handling is in Place

Use try/catch blocks and handle exceptions properly.  
- Log exceptions
- Avoid user-facing unhandled errors
- Use custom exceptions when needed

See: [Error Handling & Logging](./ErrorHandlingAndLogging.md)

> 🛠️ Ensure issues can be traced and resolved quickly.

---

## 🔹 Code is Covered by Tests

Minimum of 75% test coverage is required.  
Make sure:
- Every method has test coverage
- Positive and negative test cases exist
- Bulk test scenarios are covered

See: [Test Class Design](./TestClasses.md)

> 📌 Run all tests in target org to confirm.

---

## 🔹 Code is Reviewed and Approved

Peer review ensures code quality, readability, and alignment with team standards.  
Use a checklist or GitHub PR review template.

> 🧑‍💻 At least one reviewer should approve before deployment.

---

## 🔹 Unused Code is Removed

Remove any commented code, unused variables, or temporary debug statements.  
Keep your codebase clean and professional.

> ❌ Avoid leaving `System.debug('temp')` or commented-out logic in production.

---

## 🔹 Metadata Changes are Included

Ensure that all relevant metadata is part of the deployment package:
- Custom Fields
- Record Types
- Permission Sets
- Page Layouts

> 🧩 Metadata mismatch is a common reason for failed deployments.

---

## 🔹 Dependencies Are Deployed First

Some components depend on others (e.g., a field used in a trigger).  
Deploy in the correct order.

> ✅ Use change sets or version control tools like Gearset or Copado.

---

✅ **Summary:**
- Code is clean, safe, tested, and reviewed
- No hardcoded values
- FLS, bulk, limits, and security are respected
- Metadata and dependencies are accounted for

📌 [Back to Index](./README.md)
