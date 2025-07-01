# Test Class Design

Writing strong test classes is not just about code coverage — it’s about ensuring your logic works as expected, across edge cases and failure scenarios.
This section outlines best practices for creating clean, maintainable, and high-quality test code.

---

## 🔹 Follow Naming Conventions

Use the same name as the class being tested, with `Test` appended.

**Format:**
```
<ClassName>Test
```
**Examples:**
- `AccountTriggerHandlerTest`
- `OpportunityServiceTest`

> ✅ Helps quickly identify what the test class is covering

---

## 🔹 Maintain At Least 75% Coverage

Salesforce requires a **minimum of 75% code coverage** for deployment.
However, your goal should be **meaningful coverage**, not just hitting the number.

> ✅ Cover both positive and negative scenarios

---

## 🔹 Use `@isTest` Annotation

Mark all test classes and methods with `@isTest`.
This ensures they’re excluded from org limits and packaged deployments.

```apex
@isTest
private class AccountTriggerHandlerTest {
    @isTest
    static void testAccountInsert() {
        // test logic
    }
}
```

---

## 🔹 Separate Setup and Assertion

Keep test logic clear by splitting into:
- **Setup**: Creating test data and inputs
- **Action**: Performing the logic (e.g., insert/update)
- **Assertion**: Validating outcomes

**Example:**
```apex
@isTest
static void testUpdateStatus() {
    Account acc = new Account(Name = 'Test Co');
    insert acc;

    acc.Name = 'Updated Co';
    update acc;

    Account result = [SELECT Name FROM Account WHERE Id = :acc.Id];
    System.assertEquals('Updated Co', result.Name);
}
```

---

## 🔹 Create Utility Methods or Test Factories

Avoid repeating setup logic by using:
- Reusable test data factory classes
- Static helper methods inside test classes

**Example:**
```apex
public class TestDataFactory {
    public static Account createAccount(String name) {
        return new Account(Name = name);
    }
}
```

> ✅ Improves readability and reduces code duplication

---

## 🔹 Use `Test.startTest()` and `Test.stopTest()`

Wrap logic in `Test.startTest()` and `Test.stopTest()` to:
- Reset governor limits
- Ensure async processes are triggered

**Example:**
```apex
Test.startTest();
insert acc;
Test.stopTest();
```

---

## 🔹 Cover Bulk and Edge Scenarios

Include:
- Bulk inserts and updates (e.g., 200 records)
- Empty lists / null fields
- Exception cases

> ✅ Don’t just test the happy path

---

## 🔹 Use Meaningful Assertions

Avoid passive tests.
Use `System.assertEquals()`, `assertNotEquals()`, or `assert()` with clear intent.

**Bad:**
```apex
System.debug('Test passed');
```

**Good:**
```apex
System.assertEquals('Expected', actual);
```

---

✅ **Summary:**
- Use consistent naming (`ClassNameTest`)
- Separate setup, action, and assertions
- Use test factories for reusable setup
- Cover bulk, edge, and error cases
- Aim for meaningful coverage, not just numbers

📌 [Back to Index](./README.md)

