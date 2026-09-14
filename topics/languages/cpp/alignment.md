
# alignment

## alignas/alignof
```cpp
alignas(16) int a[4];
alignas(1024) int b[4];

assert(alignof(a) == 16);
assert(alignof(b) == 1024);
```

## struct alignment
Usually it's better to order non-static fields by largest to smallest to minimise chance of compiler adding padding (Static fields are not stored contiguous)
- order hot fields together
- cold fields a pointer dereference away
- use SoA instead of AoS

Structs are aligned according to largest single member.

On a typical system:
```cpp
struct Unoptimized {
    std::uint8_t  a;  // offset 0, size 1
                      // 7 bytes padding
    std::uint64_t b;  // offset 8, size 8
    std::uint16_t c;  // offset 16, size 2
                      // 6 bytes tail padding
};
assert(sizeof(Unoptimized) == 24);
assert(alignof(Unoptimized) == 8);

struct Optimized {
    std::uint64_t b;  // offset 0, size 8
    std::uint16_t c;  // offset 8, size 2
    std::uint8_t  a;  // offset 10, size 1
                       // 5 bytes tail padding
};
assert(sizeof(Optimized) == 16);
assert(alignof(Optimized) == 8);

// can raise alignment (but not reduce)
struct alignas(16) RaisedAlignment { 
    int b;    // 4 bytes
    char a;   // 1 byte
    char c;   // 1 byte
};
sizeof == 8
alignof == 16
```

To avoid cache thrashing, may use instead the following (C++17)
```cpp
struct Counters {
    alignas(std::hardware_destructive_interference_size)
    std::atomic<uint64_t> produced{0};

    alignas(std::hardware_destructive_interference_size)
    std::atomic<uint64_t> consumed{0};
};
```

## std::align
https://lesleylai.info/en/std-align/
`std::align` 
- returns a pointer  to the next available assignment.
- `void*& ptr` is incremented to the next available alignment
- `std::size_t& space` is decreased to `space - alignment`. **NOT** `space - alignment - size`
```cpp
void* align(
	std::size_t alignment, // desired alignment
	std::size_t size,      // size of buffer, not decremented!!!
	void*& ptr,            // ptr, incremented to next alignment
	std::size_t& space     // remaining space in buffer, decremented by alignment (and not size + alignment)
); // return  void* ptr or nullptr if failed

// roughly equivalent to 
void* align_forward(void* ptr, std::size_t alignment, std::size_t size, std::size_t space) {
    const auto addr = reinterpret_cast<std::uintptr_t>(ptr);
    const auto aligned_addr = (addr + (alignment - 1)) & ~(alignment - 1);
    const auto padding = aligned_addr - addr;

    return (space >= padding + size) 
        ? reinterpret_cast<void*>(aligned_addr) 
        : nullptr;
}
```

where `~(alignment - 1)`gives the mask without relying on 2's complement architecture
```cpp
0b00000100 // 4
0b11111100 // -4
```

![](<./assets/Pasted image 20260822154002.png>)

