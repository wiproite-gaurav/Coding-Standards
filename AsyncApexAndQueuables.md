# Asynchronous Apex & Queuables

Asynchronous Apex lets you move resource-intensive tasks outside of the current transaction.
It helps manage limits, reduce execution time, and build scalable logic for background processing.

This section covers common async techniques in Salesforce and when to use them.

---

## 🔹 Why Use Async Apex?

Async Apex allows you to:
- Bypass strict sync limits (CPU time, DML rows, callouts)
- Perform tasks after the main transaction (like updates, integrations)
- Process large volumes of records in chunks

> ✅ Great for post-save logic, batch jobs, and heavy automation

---

## 🔹 Future Methods

Use `@future` for simple, fire-and-forget logic.

**Example:**
```apex
@future
public static void notifyExternalSystem(Id accountId) {
    // perform callout or async update
}
```

### ⚠️ Limitations:
- Cannot return values
- No chaining or queue control
- Limited to primitives in parameters

---

## 🔹 Queueable Apex

Queueables offer more control than `@future` methods.
You can chain jobs, monitor execution, and pass complex data.

**Example:**
```apex
public class RecalculateTotalsQueueable implements Queueable {
    public void execute(QueueableContext context) {
        // business logic here
    }
}

System.enqueueJob(new RecalculateTotalsQueueable());
```

> ✅ Recommended for most async use cases

---

## 🔹 Batch Apex

Use Batch Apex when processing very large datasets (up to 50 million records).
Batch jobs are split into manageable chunks with separate transactions.

**Structure:**
- `start()` – query records
- `execute()` – process each chunk
- `finish()` – post-processing or chaining

**Example:**
```apex
Database.executeBatch(new MyBatchClass(), 200);
```

---

## 🔹 Scheduled Apex

Use `Schedulable` for recurring jobs (e.g., nightly cleanup, sync tasks).

**Example:**
```apex
public class DailyCleanupJob implements Schedulable {
    public void execute(SchedulableContext ctx) {
        // scheduled logic
    }
}

String cron = '0 0 1 * * ?';
System.schedule('Daily Cleanup', cron, new DailyCleanupJob());
```

> ✅ Use for time-based orchestration

---

## 🔹 Monitor Async Limits

Async operations still have limits:
- 50 Queueable jobs chained
- 5 Batch jobs active at once
- 100 callouts per transaction
- Shared org-level limits (check `Limits` class)

> ⚠️ Design responsibly in high-volume orgs

---

## 🔹 Use Cases

| Use Case                        | Method             |
|--------------------------------|--------------------|
| Call external API after save   | `@future`, `Queueable` |
| Recalculate child records      | `Queueable`        |
| Process 1M+ records            | `Batch Apex`       |
| Daily data sync job            | `Scheduled Apex`   |

---

## 🔹 Related Topics

- [Governor Limits](./GovernorLimits.md) — async helps avoid CPU timeouts
- [Deployment Checklist](./DeploymentChecklist.md) — ensure async jobs are tested and secured

---

✅ **Summary:**
- Use `@future` for simple one-off jobs
- Use `Queueable` for most scalable async logic
- Use `Batch Apex` for large-volume processing
- Use `Scheduled Apex` for time-based workflows
- Monitor limits and design for org size

📌 [Back to Index](./README.md)

