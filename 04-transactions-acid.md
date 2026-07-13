# Transactions & ACID Properties

## What is a Transaction?
```
A group of SQL operations that execute as ONE unit
Either ALL succeed → COMMIT
Or ALL fail → ROLLBACK (undo everything)

Real example — Bank Transfer ₹1000:
Step 1: Deduct from Khushbu  ✅
Step 2: Add to Amit          ❌ FAILS

Without transaction: Khushbu loses ₹1000, Amit gets nothing 😱
With transaction: ROLLBACK → both accounts unchanged ✅
```

## Syntax:
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 1000
WHERE name = 'Khushbu';

UPDATE accounts SET balance = balance + 1000
WHERE name = 'Amit';

COMMIT;    -- save permanently
-- OR
ROLLBACK;  -- undo everything
```

## ACID Properties:
```
A → Atomicity    = All or Nothing
C → Consistency  = Valid state always
I → Isolation    = Concurrent transactions don't interfere
D → Durability   = Committed data survives crashes
```

## Visual — ACID:
```
ATOMICITY:
[Deduct] + [Add] = ONE unit
Both succeed ✅ OR both fail ✅, never partial ❌

CONSISTENCY:
Before: Khushbu=5000, Amit=3000, Total=8000
After:  Khushbu=4000, Amit=4000, Total=8000 ✅
Rules always maintained (balance can't go negative)

ISOLATION:
T1: reads balance=5000
T2: updates balance=4000
T1: reads balance=5000 (still! not affected by T2)

DURABILITY:
COMMIT → written to disk
Server crashes immediately after → data still there ✅
```

## Isolation Levels:
```
READ UNCOMMITTED → can read dirty (uncommitted) data
READ COMMITTED   → only read committed data
REPEATABLE READ  → same query = same result (MySQL default)
SERIALIZABLE     → strictest, one at a time
```

## Three Read Problems:
```
Dirty Read:
T1 reads data T2 hasn't committed yet
T2 rolls back → T1 read invalid data 😱
Fix: READ COMMITTED+

Non-repeatable Read:
T1 reads row → T2 updates it → T1 reads again → different!
Fix: REPEATABLE READ+

Phantom Read:
T1 queries rows → T2 inserts new rows → T1 queries again → more rows!
Fix: SERIALIZABLE
```

## Problem Prevention by Level:
```
Level              | Dirty | Non-repeatable | Phantom
READ UNCOMMITTED   |  ❌   |      ❌        |   ❌
READ COMMITTED     |  ✅   |      ❌        |   ❌
REPEATABLE READ    |  ✅   |      ✅        |   ❌
SERIALIZABLE       |  ✅   |      ✅        |   ✅
```

## COMMIT vs ROLLBACK:
```
COMMIT:
→ Permanently saves all changes to disk
→ Transaction complete, cannot be undone
→ Other transactions can now see the changes

ROLLBACK:
→ Undoes all changes since START TRANSACTION
→ Database returns to previous state
→ Like the transaction never happened
```

## One-liner for interview:
"ACID ensures transactions are Atomic (all-or-nothing),
 Consistent (valid state always maintained), Isolated
 (concurrent transactions don't interfere via REPEATABLE READ),
 and Durable (committed data persists after crashes)"

## My Notes:
_Add your own notes here_

---
Last updated: July 2026
Asked in: Pocketful screening, general fintech interviews
