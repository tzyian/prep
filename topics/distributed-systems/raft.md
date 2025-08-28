# Raft

## Question
Explain the Raft consensus algorithm and how it ensures safety and liveness.

## Short Answer
Raft elects a leader via randomized timeouts, replicates a log to followers using AppendEntries, and commits entries once a majority acknowledges the leader’s term and index, preserving a single linearizable log prefix; liveness comes from timeouts and majority availability.

## Deep Dive
- Roles: leader, follower, candidate; terms monotonically increase.
- Leader election: randomized election timeouts reduce split votes; majority wins.
- Log replication: leader appends entries with `prevLogIndex/Term` to enforce consistency; followers reject mismatched entries.
- Commitment: an entry is committed when replicated on a quorum and the leader’s term matches; state machine applies in order.
- Safety: election restriction (leader must contain all committed entries) + log matching property.

## Follow‑ups
- Compare to [[cap-theorem]] trade‑offs.
- How snapshotting compacts logs; install snapshot RPC.

## Sources
- Diego Ongaro & Ousterhout (2014) Raft paper
- raft.github.io

