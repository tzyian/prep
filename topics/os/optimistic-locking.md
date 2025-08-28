### Optimistic vs Pessimistic Locking

#### Pessimistic Locking
- **Idea**: Assume conflicts are **likely**, so prevent them by locking resources before access.
- A process/thread **acquires a lock** before reading/writing to ensure no one else can modify it.
- Guarantees data consistency but can hurt concurrency.

**Pros**:
- Strong consistency.
- Simple reasoning.
**Cons**:
- Lower concurrency, possible deadlocks.
- High overhead if conflicts are rare.

#### Optimistic Locking
- **Idea**: Assume conflicts are **rare**, so proceed without locking.
- A process/thread reads data, does work, and **checks before writing back** whether the data was modified meanwhile.
- If a conflict is detected → abort/rollback and retry.


|Feature|Pessimistic Locking|Optimistic Locking|
|---|---|---|
|Assumption|Conflicts are likely|Conflicts are rare|
|Mechanism|Lock before read/write|Check for conflict on commit|
|Performance|Lower concurrency|Higher concurrency (if few conflicts)|
|Deadlocks|Possible|Impossible (no locks)|
|Use cases|Banking, critical consistency systems|Web apps, high-read/low-write scenarios|