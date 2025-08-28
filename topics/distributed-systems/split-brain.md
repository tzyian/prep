
## 3) Split‑Brain Scenario
**What it is:** A network partition (or control-plane failure) causes **two (or more) leaders/primaries** to exist simultaneously. Each side accepts writes, creating divergent histories and possible corruption.

**Why it’s dangerous**
- **Dual‑primary writes:** inconsistent state, lost updates on reconciliation.
- **External side effects:** duplicate payments, double‑bookings, etc.

**Prevention/mitigation**
- **Majority quorum for leadership:** only the partition with a majority can lead.
- **Fencing tokens / leases:** time‑bounded leadership with monotonic tokens to prevent old leaders from acting.
- **Witness/tie‑breaker nodes or quorum disks:** avoid 50/50 splits.
- **STONITH (“Shoot The Other Node In The Head”):** cluster-level power‑off of a suspected old primary to enforce single‑writer.
- **Synchronous replication with write quorums:** ensure only one side can commit.
