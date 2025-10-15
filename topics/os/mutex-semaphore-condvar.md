## Mutex vs Semaphore vs Condition Variable

- **Mutex (mutual exclusion lock)**: guards a *critical section* so only one thread enters at a time.
	- Ownership: thread that locks must unlock.
	- Use when you need exclusive access to shared state.

- **Semaphore**: a counter with `wait()`/`post()` that controls access to a resource pool or coordinates events.
	- **Binary semaphore** (0/1) ≈ simple gate; **counting semaphore** allows up to _N_ concurrent holders.
	- Not owned; any thread can `post()`.

- **Condition Variable**: used with a mutex to *wait for a condition/predicate* to become true.
	- Get around the need for spinlocks
	- Threads `wait()` (releasing the mutex atomically) and are awakened by `notify_one/all()`.
	- Always recheck the predicate in a **while** loop (spurious wakeups are real).
- **Condition variable**: you have a _specific predicate_ (e.g., “queue not empty”, “state == READY”) and need to sleep until it’s true. Always: `while (!predicate) cv.wait(...)`.

---

### Condvars

1. mutex MUST be locked
2. on calling: `pthread_cond_wait(&cv, &m);` 
	1. atomically unlocks mutex, sleeps
3. on wake up (signal `&cv` or spurious wakeup):
	1. return from `pthread_cond_wait(&cv, &m);` and reacquire mutex
4. check the loop again!


```c
void* waiter(void* _) {
    pthread_mutex_lock(&m);
    while (!ready) {                   // ALWAYS while, not if
	    // mutex MUST be locked
		// atomically unlocks mutex, sleeps
        pthread_cond_wait(&cv, &m);
        // on waking up (signal or spurious), 
        // reacquires mutex
        // check the loop again!
    }
	
    // ... ready is true here ...
    // do work
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


1. **Spurious wakeup case**:  
   - POSIX explicitly allows that a thread may return from `pthread_cond_wait` **even without a signal**.  
   - So the contract is: *when it returns, you must re-check your predicate under the lock*.  
   - If the predicate is still false → you go back into the wait.  



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

### 5. Summary

- Condition variable **needs a mutex** to:
    - Protect shared state (predicate).
    - Prevent missed signals.
    - Make wait/signal atomic with respect to condition checks.
        

Without the mutex, condition variables are unreliable and can lead to subtle deadlocks or lost wakeups.