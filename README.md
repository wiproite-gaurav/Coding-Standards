# Salesforce Code Standardisation Guidelines 🧠

Welcome to the **Salesforce Code Standardisation Handbook** — a practical, readable, and beginner-friendly set of guidelines for building scalable, maintainable, and enterprise-grade Salesforce code.

This handbook reflects real-world experience and internal best practices.  
Whether you're a new developer or a seasoned architect, these modular guides will help you write robust and consistent Apex, LWC, and declarative configurations across teams.  
Every point listed here is rooted in the **Zayo's original standardisation document** — **nothing is skipped or altered**.

---

## 📚 Table of Contents

### 1. [Naming Conventions](./NamingConventions.md)  
Strict rules for naming Apex classes, triggers, test classes, utility classes, variables, constants, and methods.  
Naming conventions improve maintainability, traceability, and readability across large codebases.  
We also provide do/don’t examples and naming anti-patterns.

### 2. [Trigger Best Practices](./TriggerBestPractices.md)  
Guidelines for writing clean, modular, and scalable triggers.  
Covers the "one trigger per object" principle, use of TriggerHandler classes, proper trigger context handling, and anti-patterns to avoid.

### 3. [Bulkification & Performance](./BulkSafeCode.md)  
Writing code that runs safely on large datasets is essential.  
This section discusses using collections, SOQL/DML optimization, bulk-safe patterns, and common mistakes that lead to governor limit exceptions.

### 4. [Governor Limits Handling](./GovernorLimits.md)  
Deep dive into SOQL/DML limits, CPU time, heap size, and how to avoid hitting platform limits using defensive design patterns, asynchronous calls, and context-aware execution.

### 5. [Test Class Design](./TestClasses.md)  
Effective test writing practices to ensure high code coverage and maintainable tests.  
Covers positive and negative scenario coverage, use of setup utilities, test data factory patterns, and naming conventions for test classes.

### 6. [Deployment Readiness](./DeploymentChecklist.md)  
Checklist and standards to validate before deployment.  
Includes naming, FLS enforcement, exception handling, code coverage, governor limit checks, metadata separation, and rollback safety.

### 7. [Using Constants.cls](./ConstantsClassUsage.md)  
Avoid hardcoding IDs, field values, or labels.  
Use a shared Constants.cls file instead.  
Explains what should go into Constants.cls, when to reuse vs create new constants, and how to apply it in both logic and test code.

### 8. [Custom Metadata vs Custom Settings](./CustomMetadataVsSettings.md)  
Explains when and how to use custom metadata or settings for org-level config.  
Shows how to build flexible code driven by metadata and avoid hardcoding business logic.

### 9. [Field-Level Security & CRUD Checks](./FieldLevelSecurity.md)  
Guidelines on enforcing CRUD/FLS in Apex code to respect user permissions and ensure security compliance.  
Includes utility methods, security enforcement points, and design strategies.

### 10. [Code Comments & Documentation](./CodeCommentsAndDocs.md)  
Writing meaningful comments that explain intent, not just what the code is doing.  
Covers documenting methods and classes, and keeping code clean and readable.

### 11. [Error Handling & Logging](./ErrorHandlingAndLogging.md)  
Best practices for try/catch usage, bubbling exceptions, custom exception classes, and structured logging mechanisms.

### 12. [Asynchronous Apex & Queuables](./AsyncApexAndQueuables.md)  
Patterns and use-cases for Queueable, Batchable, Future methods, and scheduled Apex.  
Best practices to offload work and avoid sync-limit failures.

### 13. [Reusable Utilities & Helper Classes](./UtilitiesAndHelpers.md)  
Organizing utility logic in common helper classes.  
Examples include string formatting, JSON parsing, SOQL-safe filters, and exception utilities.  
Emphasis on reusability and testability.

---
