## Mutex vs Semaphore vs Condition Variable

- **Mutex (mutual exclusion lock)**: guards a *critical section* so only one thread enters at a time.
  - Ownership: thread that locks must unlock.
  - Use when you need exclusive access to shared state.

- **Semaphore**: a counter with `wait()`/`post()` that controls access to a resource pool or coordinates events.
  - **Binary semaphore** (0/1) ≈ simple gate; **counting semaphore** allows up to _N_ concurrent holders.
  - Not owned; any thread can `post()`.

- **Condition Variable**: used with a mutex to *wait for a condition/predicate* to become true.
  - Threads `wait()` (releasing the mutex atomically) and are awakened by `notify_one/all()`.
  - Always recheck the predicate in a **while** loop (spurious wakeups are real).
  - **Condition variable**: you have a _specific predicate_ (e.g., “queue not empty”, “state == READY”) and need to sleep until it’s true. Always: `while (!predicate) cv.wait(...)`.

---


```c
void* waiter(void* _) {
    pthread_mutex_lock(&m);
    while (!ready)                    // ALWAYS while, not if
        pthread_cond_wait(&cv, &m);   // atomically unlocks & sleeps
    // ... ready is true here ...
    pthread_mutex_unlock(&m);
    return NULL;
}

void* signaler(void* _) {
    pthread_mutex_lock(&m);
    ready = true;
    pthread_mutex_unlock(&m);
    pthread_cond_signal(&cv);         // or pthread_cond_broadcast
    return NULL;
}
```


## Spurious Wakeups in Condvars

### Spurious Wakeups

A **spurious wakeup** happens when a thread waiting on a condition variable returns from `wait()` **even though no thread actually signaled/broadcasted**, and the condition it was waiting for may still be false.

---

#### Why do spurious wakeups occur?

1. **Performance & Implementation Simplicity**
   - Many OS kernels (Linux, BSD, macOS, Windows) implement condition variables using futexes or queues.
   - To simplify the kernel and avoid subtle deadlocks, the kernel may wake up *more threads than necessary* or sometimes *all waiters*.
   - It’s cheaper than strictly waking only the exact number of needed threads.

2. **Race Conditions in Signaling**
   - A signal may occur right before a thread goes to sleep, or multiple threads compete.  
   - To avoid missing signals, the kernel sometimes chooses to wake a thread "just in case," even if it didn’t strictly need to.

3. **Broadcasts and Interruption**
   - A `pthread_cond_broadcast` (or similar) wakes all waiting threads; not all of them will find the condition true.
   - External factors like **timeouts, cancellation, or interrupts** can also cause premature wakeups.





### Why Condition Variables Need a Mutex

#### 1. Condition Variables by Themselves
- A **condition variable (CV)** is just a signaling mechanism.  
- It allows threads to:
  - **Wait** until some condition becomes true.
  - **Notify** other threads that the condition may have changed.  

But:  
- A CV does **not** store state.  
- Without a lock, you can **miss signals** or get stuck in races.

---

#### 2. The Problem Without a Mutex
Consider two threads, **Producer** and **Consumer**:

1. Consumer checks a predicate (`queue not empty`).  
2. Predicate is false, so Consumer prepares to wait.  
3. Before it actually blocks, Producer enqueues an item and signals the CV.  
4. Signal is **lost** because Consumer wasn’t yet waiting.  
5. Consumer goes to sleep — but nobody will wake it up → **deadlock**.

This race arises if predicate check and `cv.wait()` are not atomic.

---

#### 3. Role of the Mutex
- The mutex ensures **atomicity** of:
  1. Checking the predicate.
  2. Deciding whether to wait.
  3. Releasing the lock while waiting (the `wait()` function does this atomically).
- On `cv.wait(lk, pred)`:
  - CV atomically unlocks the mutex and puts the thread to sleep.  
  - When woken, it re-locks the mutex **before returning** so the predicate can be safely rechecked.

Thus:
- No lost signals.
- No races between checking the predicate and going to sleep.
- Shared state is always accessed safely.

---

#### 4. Correct Usage Pattern
```c
pthread_mutex_t mtx = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t  cv  = PTHREAD_COND_INITIALIZER;

bool ready = false;   // shared state

void* consumer(void* arg) {
    pthread_mutex_lock(&mtx);

    // MUST use while, not if
    while (!ready) {
        pthread_cond_wait(&cv, &mtx);
        // -> atomically unlocks 'mtx' and sleeps
        // -> when woken, 'mtx' is re-locked before returning
    }

    printf("Consumer: condition met!\n");
    pthread_mutex_unlock(&mtx);
    return NULL;
}

void* producer(void* arg) {
    pthread_mutex_lock(&mtx);
    ready = true;                // update predicate
    pthread_mutex_unlock(&mtx);
    pthread_cond_signal(&cv);    // wake one waiting thread
    printf("Producer: signaled condition\n");
    return NULL;
}
```

#### 5. Summary

- Condition variable **needs a mutex** to:
    
    - Protect shared state (predicate).
        
    - Prevent missed signals.
        
    - Make wait/signal atomic with respect to condition checks.
        

Without the mutex, condition variables are unreliable and can lead to subtle deadlocks or lost wakeups.



#### 1. Shared State Must Be Protected
- The condition variable (`pthread_cond_t`) itself does **not track the condition** (like `ready == true`).
- The **predicate** is stored in shared memory (`ready`).
- Without a mutex:
  - Producer could write `ready = true` at the same time Consumer reads it.
  - This creates a **data race** → undefined behavior in C.

---

#### 2. Wait Must Be Atomic
- Correct behavior requires that **checking the predicate** and **blocking on the CV** happen atomically.
- If no mutex:
  1. Consumer checks `ready == false`.
  2. Producer sets `ready = true` and signals CV.
  3. Consumer goes to sleep.  
  → **Signal lost**; consumer will sleep forever.