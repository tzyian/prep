# Lamport Time

## Question
What are Lamport clocks and how do they differ from vector clocks?

## Short Answer
Lamport clocks assign a monotonically increasing scalar per event that preserves happens‑before but cannot detect concurrency; vector clocks use a per‑node vector to both preserve happens‑before and detect concurrent events.

## Deep Dive
- Lamport: increment on event; include timestamp on send; set to max(received, local)+1.
- Vector: merge element‑wise max; A ≤ B iff every component A[i] ≤ B[i] and one is strictly smaller.

## Follow‑ups
- Use in [[distributed-systems/consistency-models]] causal ordering.

