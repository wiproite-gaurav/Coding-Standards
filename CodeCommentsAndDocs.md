# Code Comments & Documentation

Code should be easy to understand — even without the original developer around.
Proper use of comments and documentation helps teams onboard faster, debug efficiently, and reduce misunderstandings.
This section outlines when and how to write clear, purposeful comments.

---

## 🔹 Comment the *Why*, Not the *What*

Avoid restating what the code does — explain why it’s doing it.

**Bad:**
```apex
// Increment counter
counter++;
```

**Good:**
```apex
// Increment retry counter to prevent infinite loop after 3 failures
counter++;
```

> ✅ Focus on explaining business rules, exceptions, and intentions

---

## 🔹 Use Block Comments for Sections

For larger logic blocks or unusual flows, use multi-line comments to explain the overall approach.

**Example:**
```apex
/*
  This block updates lead status only if:
  - Lead is older than 30 days
  - No contact has been logged
*/
```

> 📌 Helps future devs understand decision context quickly

---

## 🔹 Document Public Methods and Classes

Use doc-style comments to explain what a method does, its inputs, and expected outputs.

**Example:**
```apex
/**
 * Validates account ownership
 * @param acc Account to check
 * @param userId Owner to validate against
 * @return Boolean true if match
 */
public static Boolean validateOwnership(Account acc, Id userId) {
    return acc.OwnerId == userId;
}
```

> 📚 Great for shared utilities and service classes

---

## 🔹 Avoid Excessive or Outdated Comments

Remove:
- Commented-out code from past versions
- TODOs that are already done
- Explanations for trivial logic

> ❌ Avoid clutter — comments should add value, not noise

---

## 🔹 Use Inline Comments Sparingly

For complex logic lines, short inline comments are helpful.
But overusing them reduces readability.

**Example:**
```apex
decimal fee = amount * 0.02; // 2% processing fee
```

> ✅ Use them where a quick hint clarifies the line

---

## 🔹 Don’t Comment Around Bad Code — Fix It

If you feel the need to explain what a line does, consider rewriting it to be clearer instead.

**Before:**
```apex
// Calculate total after tax and discount
decimal amt = a * 1.18 - (a * 0.05);
```

**After:**
```apex
decimal taxAmount = baseAmount * 0.18;
decimal discount = baseAmount * 0.05;
decimal total = baseAmount + taxAmount - discount;
```

> ✨ Clean code reduces the need for comments

---

## 🔹 Related Sections

- [Naming Conventions](./NamingConventions.md) — choose names that reduce the need for comments
- [Test Class Design](./TestClasses.md) — document test scenarios and intent where necessary

---

✅ **Summary:**
- Comment intent, not behavior
- Use doc-comments for public methods and classes
- Clean up outdated or redundant comments
- Write self-explanatory code where possible

📌 [Back to Index](./README.md)

