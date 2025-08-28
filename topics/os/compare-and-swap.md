
#### 1. Compare-and-Swap (CAS)
- **Definition**: An atomic instruction that compares a memory location’s value to an expected value and, only if they match, updates it to a new value.  
- Used heavily in **lock-free algorithms**.
#### 2. Test-and-Set (TAS)

- **Definition**: An atomic instruction that sets a memory location to `1` and returns its old value.
    
- Typically used to build **spinlocks**.


```c
#include <stdatomic.h>
#include <stdbool.h>

//// --- Compare-and-Swap (CAS) ---
bool cas(int *addr, int expected, int new_val) {
    return __atomic_compare_exchange_n(
        addr,                // memory location
        &expected,           // expected value (updated if fails)
        new_val,             // new value to set
        false,               // don’t allow spurious failures
        __ATOMIC_SEQ_CST,    // memory order for success
        __ATOMIC_SEQ_CST     // memory order for failure
    );
}

//// --- Test-and-Set (TAS) ---
int tas(int *addr) {
    return __atomic_test_and_set(
        addr,                // memory location (treated as a flag/bit)
        __ATOMIC_SEQ_CST     // memory order
    );
}


```
#### 