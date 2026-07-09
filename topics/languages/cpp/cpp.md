
Immediately Invoked Function


# Memory Model
see 3211 notes

TODO: parse
```
Yes — here’s a structured list of what this thread has covered so far.

## Core qualifiers

- `const`
    
- `constexpr`
    
- `static`
    
- `inline`
    
- `volatile`
    
- `private`
    

These came up in the context of what they mean, when they are needed, and where they are typically placed.

## Pointers and const forms

- `const char*`
    
- `char* const`
    
- `const char* const`
    
- Why string literals require `const char*`
    
- How `constexpr` interacts with pointer types
    
- Reading `const` declarations right-to-left
    

## String-related types

- `char[]`
    
- `const char*`
    
- `std::string`
    
- `std::string_view`
    
- Brief mention of wider string variants like `std::wstring`
    
- Owning vs non-owning string representations
    
- C-style strings vs standard library string types
    

## Storage and placement

- Putting constants in a class as `private static constexpr`
    
- Putting constants in headers
    
- Putting constants in `.cpp` files
    
- Using anonymous namespaces for file-local names
    
- Header-defined functions and variables
    
- File-local implementation details
    

## Move and forwarding

- `std::move`
    
- When to use `std::move`
    
- When not to use `std::move`
    
- Why `std::move` does not itself move
    
- Moved-from object state
    
- `std::forward`
    
- Forwarding references
    
- Preserving lvalue/rvalue category in templates
    
- Difference between `move` and `forward`
    

## Return optimizations

- NRVO
    
- Copy elision
    
- Why `return local;` is preferred over `return std::move(local);` for local returns
    
- Guaranteed elision in modern C++ contexts
    

## Special member rules

- Rule of three
    
- Rule of five
    
- Rule of zero
    
- When custom destructor / copy / move members are needed
    
- Resource ownership and RAII motivation behind these rules
    

## Object lifetime and ownership

- RAII
    
- Resource-managing classes
    
- `std::unique_ptr`
    
- `std::shared_ptr`
    
- Converting `unique_ptr` to `shared_ptr`
    
- Why conversion requires `std::move`
    
- Why `shared_ptr` does not generally convert back to `unique_ptr`
    
- Ownership transfer vs shared ownership
    

## Constructors and initialization

- Constructor member initializer lists
    
- Actual member initialization order
    
- Declaration order in the class definition
    
- Not initializer-list order
    
- Not constructor parameter order
    
- Base class initialization order
    
- Constructor body runs after base/member initialization
    
- Why compilers warn about mismatched init-list order
    

## Templates

- What templates are
    
- Function templates
    
- Class templates
    
- Type deduction
    
- Template instantiation
    
- Template specialization
    
- Why template definitions are usually placed in headers
    
- Templates as compile-time generic programming rather than runtime polymorphism
    

## Inheritance and virtual behavior

- Base classes
    
- Polymorphic use
    
- Virtual destructors
    
- Why a base class destructor should be virtual when deleting through base pointers
    
- Destruction order from derived to base
    
- Interface-like base class pattern with `virtual ~Base() = default`
    

## Parsing and syntax traps

- Most vexing parse
    
- Declarations that look like object construction
    
- Using brace initialization to avoid parsing surprises
    

## Memory and concurrency

- C++ memory model
    
- Data races
    
- Undefined behavior from unsynchronized concurrent access
    
- Atomics and synchronization as the real tools for multithreading
    
- Why `volatile` is not a thread-synchronization mechanism
    
- Basic mention of memory ordering concepts
    

## File I/O

- `std::ifstream`
    
- Reading files in idiomatic C++
    
- `std::ofstream`
    
- `std::fstream`
    
- Text vs binary file reading
    
- Opening files in constructors or via `open()`
    
- `std::getline`
    
- RAII-based automatic closing of file streams
    

## Other concepts mentioned

- Value categories: lvalue, rvalue, prvalue/xvalue at a high level
    
- Smart pointers generally
    
- Ownership semantics
    
- Copy vs move behavior
    
- Access control with `private`
    
- Internal linkage
    
- One-definition-rule-related use of `inline`
    

## Repeated practical rules that came up

- Prefer `std::string` for owned text
    
- Prefer `std::string_view` for read-only non-owning text
    
- Use `const char*` mainly for C interop or string literals
    
- Use `std::move` only when you are done with the source object
    
- Use `std::forward` only in forwarding-template situations
    
- Use `inline` for header definitions, not as a manual speed switch
    
- Use anonymous namespace for `.cpp`-local helpers/constants
    
- Use `private static constexpr` for class-specific constants
    
- Use a virtual destructor in a polymorphic base class
    
- Keep initializer-list order matching declaration order for readability and safety
    

## Small code patterns discussed

- `private static constexpr const char*`
    
- `private static constexpr std::string_view`
    
- `return local;` instead of `return std::move(local);`
    
- `std::shared_ptr<T> sp = std::move(up);`
    
- `std::ifstream in("file.txt");`
    
- Template examples for functions and classes
    
- Constructor init-order examples
    
- Virtual destructor base-class examples
    

If you want, I can turn this into:

1. a clean study checklist,
    
2. a grouped cheat sheet, or
    
3. a likely-interview-topics subset.
```


# Difference between strings

- `const char* name = "x";`
    
- `constexpr const char* name = "x";`
    
- Better in modern C++: `constexpr std::string_view name = "x";`cppreference+2
- but it requires heap allocated string

# anonymous namesapce

# lvalues, rvalues, xvalues, etc


# std::move, std::forward


# inline


# template
```cpp

template <typename T>
T add(T a, T b) {
    return a + b;
}

template <typename T>
class Box {
    T value;
public:
    Box(T v) : value(v) {}
};
```

# lambda


# ifstream

Never open() a file manually

# virtual dtor


# uniq to shptr
```cpp
std::unique_ptr<Foo> up = std::make_unique<Foo>();
std::shared_ptr<Foo> sp = std::move(up);
std::shared_ptr<Foo> sp = up;   // wrong
```

# const function
```cpp
class X {
public:
    int size() const;
};
```



# macros

# const ptrs
`const` always refers to the thing on the left, unless its the leftmost
```cpp
const char* p;      // (pointer to) const char
char const* p2;     // same as above
char* const p3 = s; // const pointer (to char)
const char* const p4 = "hi"; // (const pointer) to (const char)
```


# consteval

```cpp
// NOTE:
// consteval is CPP20. CPP17 can use constexpr instead
// consteval is guaranteed compile
// init_pow() has to be defined outside because members are not fully defined
// until after class defintion is finished, so pow cannot be constexpr if this
// is defined within the class static consteval array<int, MAX> init_pow() {

// NOTE: good practice to default initalise pow with {}
// to force array to be of 0s rather than garbage

// std::array<int, MAX> p{};
// static consteval std::array<int, MAX> init_pow() {
//     p[0] = 1;
//     for (int i = 1; i < MAX; ++i)
//         p[i] = p[i - 1] * 10LL % MOD;
//     return p;
// }

class Solution {
  private:
    // NOTE: standard practice is to put static before other modifier
    static constexpr int MOD = 1'000'000'007;
    static constexpr int MAX = 1'00'001;

    // immediate invoked lambda expression (IIFE)
    static constexpr std::array<int, MAX> pow = []() consteval {
        std::array<int, MAX> p{};
        p[0] = 1;
        for (int i = 1; i < MAX; ++i) {
            p[i] = (p[i - 1] * 10LL) % MOD;
        }
        return p;
    }();

	// runs on first runtime call
    // static inline array<int, MAX> pow{}
    // static inline int init = []() {
    //     pow[0] = 1;
    //     for (int i = 1; i < MAX; ++i)
    //         pow[i] = pow[i - 1] * 10LL % MOD;
    //     return 0;
    // }();
```