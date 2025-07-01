# Bulkification & Performance

Salesforce operates in a multi-tenant environment, and enforcing governor limits is critical to system stability.  
Writing **bulk-safe** code ensures that your logic works reliably even when handling large data volumes — like imports, API updates, or batch jobs.

This section explains how to write scalable, efficient Apex code that respects Salesforce limits.

---

## 🔹 What is Bulkification?

Bulkification means writing code that can handle **multiple records** at once instead of assuming only one.
Salesforce may invoke your logic with up to 200 records in a single transaction.

> ❌ Avoid code that only works for a single record.
> ✅ Always use loops, maps, and collections.

---

## 🔹 Never Write SOQL/DML Inside Loops

This is the **most critical rule** in writing efficient Salesforce code.
Running SOQL or DML inside a loop can easily exceed governor limits.

**Bad Practice:**
```apex
for (Account acc : Trigger.new) {
    Contact c = [SELECT Id FROM Contact WHERE AccountId = :acc.Id LIMIT 1];
}
```

**Good Practice:**
```apex
Set<Id> accountIds = new Set<Id>();
for (Account acc : Trigger.new) {
    accountIds.add(acc.Id);
}

Map<Id, List<Contact>> accountIdToContacts = new Map<Id, List<Contact>>();
for (Contact c : [SELECT Id, AccountId FROM Contact WHERE AccountId IN :accountIds]) {
    if (!accountIdToContacts.containsKey(c.AccountId)) {
        accountIdToContacts.put(c.AccountId, new List<Contact>());
    }
    accountIdToContacts.get(c.AccountId).add(c);
}
```

---

## 🔹 Use Collections: Sets, Maps, Lists

Collections allow you to store, group, and process data efficiently.

**Examples:**
- Use `Set<Id>` for filtering or deduplicating IDs
- Use `Map<Id, SObject>` to avoid repeated SOQL calls
- Use `List<SObject>` to collect records for DML

> ✅ Design your logic around collection handling, not individual records

---

## 🔹 Query Efficiently

Only retrieve fields you need. Avoid `SELECT *`-style queries (even if possible via dynamic SOQL).

**Example:**
```apex
[SELECT Id, Name, OwnerId FROM Account WHERE Id IN :accountIds]
```

> ✅ Always use selective filters to improve performance

---

## 🔹 Combine DML Statements

Group multiple record updates or inserts into a single DML statement.

**Bad Practice:**
```apex
for (Account acc : accountsToUpdate) {
    update acc;
}
```

**Good Practice:**
```apex
update accountsToUpdate;
```

> 📌 This reduces DML calls and improves execution time

---

## 🔹 Leverage Trigger Context Variables

Instead of querying again, use `Trigger.new`, `Trigger.oldMap`, and other context variables for available data.

**Example:**
```apex
for (Id accId : Trigger.oldMap.keySet()) {
    Account oldAcc = Trigger.oldMap.get(accId);
    Account newAcc = Trigger.newMap.get(accId);
    if (oldAcc.Status__c != newAcc.Status__c) {
        // status changed — do something
    }
}
```

> ✅ Avoid querying what’s already available

---

## 🔹 Use Helper Methods for Reusability

If logic repeats across triggers or flows, move it to a utility or service class.
This makes code more testable and reduces duplication.

> ✅ Extract logic from loops into helper methods when needed

---

✅ **Summary:**
- Handle multiple records using collections
- Never place SOQL/DML inside loops
- Query only necessary fields
- Use maps and sets for lookups and grouping
- Keep DML calls outside loops

📌 [Back to Index](./README.md)

