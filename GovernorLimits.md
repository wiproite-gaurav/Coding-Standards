# Governor Limits Handling

Salesforce imposes strict governor limits to ensure multi-tenant performance and stability.
Every piece of Apex code must be designed to stay within these limits — especially when handling large datasets or complex transactions.

This section provides strategies to respect these limits while keeping your logic clean and efficient.

---

## 🔹 Understand Key Limits

Some of the most important Apex governor limits:

| Limit Type                  | Per Transaction |
|----------------------------|-----------------|
| Total SOQL queries         | 100             |
| Total DML statements       | 150             |
| CPU time                   | 10,000 ms       |
| Heap size                  | 6 MB (sync)     |
| Callouts                   | 100             |
| Future calls               | 50              |

> 📌 These limits apply per transaction — across all triggers, flows, and processes

---

## 🔹 Avoid SOQL and DML Inside Loops

Running queries or DML statements inside a loop is the most common cause of hitting limits.

✅ **Move SOQL and DML outside of loops** and use collections instead.

Refer to [BulkSafeCode.md](./BulkSafeCode.md) for examples.

---

## 🔹 Use Maps for Efficient Access

Maps reduce the need for repeated SOQL lookups and make your code faster.

**Example:**
```apex
Map<Id, Account> accountsById = new Map<Id, Account>(
    [SELECT Id, Name FROM Account WHERE Id IN :accountIds]
);
```

> ✅ Use maps when you need to link related records or check existing data

---

## 🔹 Filter Records Before DML

Only perform DML on records that actually need updates.
This reduces unnecessary processing and helps stay within DML limits.

**Example:**
```apex
List<Account> accountsToUpdate = new List<Account>();
for (Account acc : Trigger.new) {
    if (acc.Name == null) {
        acc.Name = 'Default';
        accountsToUpdate.add(acc);
    }
}

if (!accountsToUpdate.isEmpty()) {
    update accountsToUpdate;
}
```

---

## 🔹 Break Logic Using Asynchronous Processing

If your logic is heavy or long-running, consider moving it to an async context:
- `@future` methods
- `Queueable` Apex
- `Batch Apex`
- `Scheduled Apex`

**Example:**
```apex
System.enqueueJob(new MyQueueableClass(recordIds));
```

> ✅ Offloading to async helps bypass synchronous limits like CPU time or heap size

---

## 🔹 Monitor CPU Time

Complex logic or nested loops can cause CPU timeouts (10,000 ms limit).
Avoid deep nesting and repeated calculations.
Break logic into smaller chunks or offload to async when needed.

---

## 🔹 Minimize Heap Size

Be cautious with large datasets, strings, and attachments.
Avoid holding unnecessary data in memory.
Use limits class to monitor usage:

```apex
System.debug('Current Heap: ' + Limits.getHeapSize());
```

> ✅ Streamline your object structures and avoid storing entire queries when not needed

---

## 🔹 Use Limits Class for Debugging

Salesforce provides the `Limits` class to help you inspect usage.

**Examples:**
```apex
System.debug('SOQL queries used: ' + Limits.getQueries());
System.debug('DML rows used: ' + Limits.getDmlRows());
```

> 🧪 Useful in testing and debugging governor usage

---

✅ **Summary:**
- Know your governor limits and plan accordingly
- Avoid SOQL/DML in loops
- Use maps and filters to reduce operations
- Offload heavy logic to async
- Monitor and optimize heap and CPU usage

📌 [Back to Index](./README.md)

