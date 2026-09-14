# String slicing
NOTE THE PARAMS USE LENGTH AND NOT END INDEX!!!!

```cpp
std::string substr(size_t pos = 0, size_t len = npos) const;

// use as follows:
s.substr(start_idx, substr_length);
s.substr(start_idx, end_idx - start_idx + 1);
```

# Vectors
`push_back` can cause pointer invalidation if `push_back` causes resizing
use `std::vector::reserve(N)` or `std::vector<std::unique_ptr<T>>`
Note that iterators are invalidated as is usual for most languages

Reminder that `vector<bool>` is not a vector

```cpp
vector<int> v(n);    // vector of size n, filled with 0s
vector<int> v(n, 1); // vector of size n, filled with 1s
auto vector=vector(n, vector(size, value);
vector v{0,1,2,3};   // a vector {0, 1, 2, 3}
v.assign(4, 7);      // change v to a 4 element vector with ALL elements 7

// footgun
v.resize(5, 9);      // AVOID!! old elements remain, extra elements are truncated, new elements are filled with 9

// more footguns
vector<string> foos{5}; // {"", "", "", "", ""}
vector<int>    bars{5}; // {5}
```
# Emplace_back
As a matter of style, just use `push_back` for everything because there's way too many footguns due to template instantiation/surprising implicit conversions
```cpp
vector<deque<int>> vd;
vd.push_back({4}); // push a deque {4}
vd.emplace_back(4); // deque(4) constructs a deque {0,0,0,0} 
vector<pair<int, int>> vp;
vp.push_back({2, 3}); // push a pair {2, 3}
vp.emplace_back(2, 3); // push a pair {2, 3}

struct Widget {	Widget(int) }
vector<Widget> ws;
ws.emplace_back(3.9); // silently narrows 3.9 to 3 then Widget(3)
ws.push_back(3.9);    // compile error

ws.emplace_back(ws[0]) // v[0] can be invalidated on reallocation
ws.push_back(ws[0])    // materialises
```

# Erase-remove
```cpp
// before
vec.erase(std::remove_if(vec.begin(), vec.end(), [](int x) { return x % 2 != 0; }), vec.end());

// C++20
std::erase_if(vec, [](int x) { return x % 2 != 0; });
```
# Ownership
```cpp
for (char c : make_strings()[0]) {  // dangling
    use(c);
}
// DO INSTEAD:
auto strings = make_strings()[0]
```

# Perf Timing
```cpp
#include <chrono>

const auto start = std::chrono::steady_clock::now();

work();

const auto end = std::chrono::steady_clock::now();
const auto elapsed =
    std::chrono::duration_cast<std::chrono::microseconds>(end - start);

std::cout << elapsed.count() << " us\n";
```

# Reading files
```cpp
#include <fstream>
#include <string>

std::ifstream file("data.txt");
if (!file) {
    std::cerr << "Error opening file" << std::endl;
}

// Read lines
std::string line;
while (std::getline(file, line)) {
    std::cout << line << std::endl;
}

// Slurp whole file
std::ostringstream buffer;
buffer << file.rdbuf();
std::string file_contents = buffer.str();
```

# Anonymous namespace


Anonymous namespace restricts visibility to this file
```cpp
namespace {
	int helper() { return 42; };
	// stuff that only applies within the file
	
} // namespace
```


# Header vs Cpp anonymous namespace vs within class
TODO:
```cpp
// foo.h
class Foo {
    static constexpr std::string_view kName = "hello";
};
// foo.cpp
namespace {
	constexpr int kBufferSize = 4096;
} // namespace
```

# Const
`const` always applies to the thing on its left, unless leftmost
```cpp

const char* p;      // (pointer to) const char
char const* p2;     // same as above
char* const p3 = s; // const pointer (to char)
const char* const p4 = "hi"; // (const pointer) to (const char)

int x = 10;
int& r1 = x;          // reference to non-const int
const int& r2 = x;    // reference to const int (or: int const& r2)
// r1 can be modified, but not r2
```

const parameters
```cpp
class Buffer {
	int* getData();              // (A) for non-const Buffer
	const int* getData() const;  // (B) for const Buffer
}

void foo(const Buffer& b, Buffer& b2) {
    // non-const, modifiable
	int* p2 = b.getData();              // OK: calls (A)
	*p2 = 42;                           
    // const, no modifiable
    const int* p = b.getData();         // OK: calls (B)
}
```

**const functions**

```cpp
const int* const Buf::getData() const
│        │                └─ (A) function is const, can only be called on const buf
│        └─ (B) pointer itself is const (doesn't affect caller)
└─ (C) pointed-to int is const
```

```cpp
const int* const Buffer::getData() const {
// (C)      (B)                     (A)
    const int* const p = data;  // p is a const pointer to const int
    // p = nullptr;             // ERROR: p is const (B)
    // *p = 10;                 // ERROR: *p is const int (C)
    return p;
}

auto* p = buf.getData();        // caller
p = nullptr;                    // prvalue can be modified despite (B)
```


# Immediately Invoked Function
Used especially to maintain `const` correctness
can be replaced with `consteval` (CPP20)
```cpp
const Widget my_widget = [&]() { 
	if (use_cache) {
		return load_from_cache(); 
	} 
	auto raw_data = download_payload(); 
	return parse_network_data(raw_data); 
}(); // Executed immediately
```
# consteval
| constexpr (C++17)       | consteval (C++20)                   |
| ----------------------- | ----------------------------------- |
| Functions and variables | Functions only                      |
| Compile time or runtime | Compile time                        |
| Implicitly `inline`     | Implicitly `inline` and `constexpr` |

Below, `init_pow()` has to be defined outside because members are not fully defined until after class defintion is finished, so `pow` cannot be `constexpr` if this is defined within the class 

```cpp

// NOTE: good practice to default initalise pow with {}
// to force array to be of 0s rather than garbage
std::array<int, MAX> p{};
static consteval std::array<int, MAX> init_pow() {
    p[0] = 1;
    for (int i = 1; i < MAX; ++i)
        p[i] = p[i - 1] * 10LL % MOD;
    return p;
}

class Solution {
  private:
    static constexpr int MOD = 1'000'000'007;

    // immediate invoked lambda expression (IIFE)
    // runs on first runtime call
    static constexpr std::array<int, MAX> pow = []() consteval {
        std::array<int, MAX> p{};
        p[0] = 1;
        for (int i = 1; i < MAX; ++i) {
            p[i] = (p[i - 1] * 10LL) % MOD;
        }
        return p;
    }();

	// runs on first runtime call
    static inline array<int, MAX> pow{}
    static inline int init = []() {
        pow[0] = 1;
        for (int i = 1; i < MAX; ++i)
			pow[i] = pow[i - 1] * 10LL % MOD;
        return 0;
    }();
```

# `friend` cpp
by adding `friend foo()` or `friend clazz` within a class `Clar`, `foo` and `clazz` can access private and protected members in class allows accessing private and protected members of `Clar`

# `inline`
use when defining functions or variables in headers across multiple translation units
```cpp
// within the header
inline int add(int a, int b) { return a + b; }
inline constexpr const char* kName = "hello";
```

# `static`

| Type                                  | Meaning                                                                      |
| ------------------------------------- | ---------------------------------------------------------------------------- |
| `static` vars within class            | shared across all objects (same as Java)                                     |
| `static` vars within a function:      | has program lifetime, but visibility only within the function (same as Java) |
| `static` methods within class         | class level not object level (same as Java)                                  |
| `static` vars/functions at file level | internal inkage (only visible in this file). Same as anon namespace          |
# `volatile`
- use for objects whose value may change outside normal program flow like memory-mapped hardware
- if you have to use, think about why you have to use it


# Strings
https://devblogs.microsoft.com/oldnewthing/20230803-00/?p=108532

`std::string` allocates on heap when exceeding small string optimisation (SSO) buffer.

FYI `k` stands for constant
Strings are static storage on the read-only data segment
`std::string_view` is non-owning view storing a `const char*`
```cpp
namespace {
	// stack allocated
	constexpr const char* kName = "hello";
	constexpr std::string_view name = "x";
} // namespace
```

# Lambdas

| **Capture Syntax** | **Description**                                              | **Example**                       |
| ------------------ | ------------------------------------------------------------ | --------------------------------- |
| `[]`               | No variables are captured.                                   | `[](){ return 42; }`              |
| `[x]`              | Captures `x` by value (read-only copy).                      | `[x](int y){ return x + y; }`     |
| `[&x]`             | Captures `x` by reference (can modify the original).         | `[&x](){ x++; }`                  |
| `[=]`              | Captures all automatic local variables by value.             | `[=](){ return a + b; }`          |
| `[&]`              | Captures all automatic local variables by reference.         | `[&](){ a++; b++; }`              |
| `[=, &x]`          | Captures all by value, but `x` by reference.                 | `[=, &x](){ x += a; }`            |
| `[this]`           | Captures the class instance pointer to access class members. | `[this](){ this->memberFunc(); }` |
```cpp
auto func = [&](int y){
	return x + y;
}


std::sort(nums.begin(), nums.end(), [](int a, int b) { 
	return a > b; 
});

```



# Attributes

`[[nodiscard]]`  warns if return is discarded
`[[fallthrough]]` for switch statements
`[[likely]]` and `[[unlikely]]` for perf optimisation in branches
```cpp
[[nodiscard]] int calculate_secure_hash() { return 42; }

[[nodiscard]] enum class ErrorCode { Success, Timeout, Failure };

// to silence warning
std::ignore = calculate_secure_hash()
(void) calculate_secure_hash()'


switch (mode) {
	case Mode::Fast: {
		setup_fast_path();
		[[fallthrough]];
	}
	case Mode::Normal: {
		execute_common_logic(); 
		break; 
	}
}

if (ptr == nullptr) [[unlikely]] {
	return Error::NullPointer;
}
```


# Noexcept

```cpp
    Buffer(Buffer&& other) noexcept
        : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }
```
`noexcept` calls `std::terminate` is called if an exception is not caught within the function
Use as API contract guarantee 
Esp move ctor/assm, dtors


# `std::shared_ptr`
Prefer `std::make_shared` and `std::make_unique`
```cpp
struct Foo {
	int value = 100;
	void print() { std::cout << "Printed!\n"; } 
};

// GOOD
auto shptr = std::make_shared<int>(42);
auto uptr = std::make_unique<Foo>(42.0);
std::cout << *shptr << std::endl; // 42
std::cout uptr->print() << std::endl; // Printed!

// BAD
int* raw = new int(10);
std::shared_ptr<int> ptr1(raw);
std::shared_ptr<int> ptr2(raw); // double-free on dtor
```

Unique pointers can be converted to shared pointers
But not vice versa 
```cpp
std::unique_ptr<Foo> up = std::make_unique<Foo>();
std::shared_ptr<Foo> sp = std::move(up);
// std::shared_ptr<Foo> sp = up;   // ERROR
```
# Designated Initializers (C++20) 

Lets you have better compile time safety where the ordering must be kept unlike ctor initialiser list

```cpp
struct Foo {
	Bar bar;
	int a = 2;
	int b;
}

Foo foo {
	.bar = Bar{};
	// a is omitted and takes its default value
	.b = 1
}
```



# Casts

## `static_cast`
```cpp
int x = 10;
double y = static_cast<double>(x);
```

Upcast 
```cpp
struct Base {};
struct Derived : Base {};
Derived d;
Base* base = static_cast<Base*>(&d);  // upcast
```

Downcast
```cpp
Base* base = /* ... */;
Derived* derived = static_cast<Derived*>(base); // downcast with no type checking
```

## `dynamic_cast`
Use it for checked conversions in polymorphic class hierarchies. The base class must have at least one virtual function, commonly a virtual destructor.
```cpp
struct Base {
    virtual ~Base() = default;
};

struct Derived : Base {
    void do_something() {}
};

Base* base = new Derived;

if (Derived* derived = dynamic_cast<Derived*>(base)) {
	// cast returns ptr if object is Dervied or nullptr
    derived->do_something();
}

delete base;
```


## `const_cast`
to add or remove `const` or `volatile`, but removing const doesn't make objects mutable
```cpp
const int value = 42;
const int* const_ptr = &value;
int* ptr = const_cast<int*>(const_ptr);

const int value = 42;
int* ptr = const_cast<int*>(&value);
*ptr = 100;  // UB, cannot modify `const int value`

int value = 42; // non-const
const int* const_ptr = &value;
int* ptr = const_cast<int*>(const_ptr);
*ptr = 100;  // OK, modifies non-const value
```

## `std::dynamic_pointer_cast<T>`
```cpp
std::shared_ptr<ChildB> ptrChild = std::dynamic_pointer_cast<ChildB>(ptrBase);
if (ptrChild) {
    ptrChild->B(20);
    std::cout << "use count A: " << ptrBase.use_count() << std::endl;
    std::cout << "use count B: " << ptrChild.use_count() << std::endl;
```


## `reinterpret_cast`
to force a cast
```cpp
int value = 42;
char* bytes = reinterpret_cast<char*>(&value);
```
## `bit_cast` (C++20)
```cpp
#include <bit>
float f = 1.0f;
std::uint32_t bits = std::bit_cast<std::uint32_t>(f);
```

# Enum/enum class
enums leak out and are implicitly 0-based integers
enum classes don't leak, must be casted
```cpp
enum TrafficLight { Green, Yellow, Red }; 
// values leak out
// implicitly are 0 based integers

enum class TrafficLight { Green, Yellow, Red }; 
TrafficLight::Red
```

# Union/Variant

## Variant

Prefer `std::variant` to handle ctor and dtor for you
```cpp
#include <string>
#include <variant>

using Data = std::variant<int, std::string>;
Data a = std::string{"hello"};
a = 100;                         // destroys string automatically
a = std::string{"world"};        // constructs string automatically
```

## Union  
No C-style type punning in CPP

Basic types only:
```cpp
struct Value {
    enum class Kind { Integer, Decimal };
    Kind kind; // tag so you don't UB by using the wrong field
    union { int integer, double decimal };
    explicit Value(int x) : kind(Kind::Integer), integer(x) {}
    explicit Value(double x) : kind(Kind::Decimal), decimal(x) {}
};
Value value(42);
value.decimal = 3.14f;
value.kind = Value::Kind::Decimal;
```

If non-basic types, must have dtor
```cpp
struct Value
	enum class Kind { Integer, std::string}
	Kind kind;
    union {
        int integer;
        std::string text;
        
        Storage() : number(0) {}
        ~Storage() {}
    } storage;
    
    Data() { ... }
    explicit Data(int value) { ... }
    
    explicit Data(std::string value) : kind(Kind::Text) {
	    new (&storage.text) std::string(std::move(value)); 
	}
	
    ~Data() {
     if (kind == Kind::Text)
	     storage.text.~basic_string(); 
	}
};
```


# Rule of 3/5/0
0 means use RAII

ctor
1. dtor
2. copy ctor
3. copy assignment
4. move ctor
5. move assignment

```cpp
class Buffer {
    int* data;
    std::size_t size;

public:
    // 1. Constructor
    explicit Buffer(std::size_t s) 
	    : size(s)
	    , data(s == 0 ? nullptr : new int[s]) {}
	    
    // 2. Destructor
    ~Buffer() { delete[] data; }

    // 3. Copy constructor (deep copy)
    Buffer(const Buffer& other)
        : size(other.size)
        , data(other.size == 0 ? nullptr : new int[other.size]) {
        if (size != 0) {
            std::copy(other.data, other.data + size, data);
        }
    }

    // 4. Move constructor
    Buffer(Buffer&& other) noexcept
        : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }

    // 5. Copy/move assignment via copy-and-swap
    Buffer& operator=(Buffer other) {
        std::swap(data, other.data);
        std::swap(size, other.size);
        return *this;
    }
};
```



# Polymorphism
if a class has any virtual functions, give it a virtual destructor too.
```cpp
struct Shape { // pure virtual class is an interface
    virtual double area() const = 0;  // pure virtual
    virtual ~Shape() = default;       // virtual destructor
};

struct Rectangle : Shape {
    double w, h;
    Rectangle(double w_, double h_) : w(w_), h(h_) {}
    double area() const override { return w * h; }
};
```
# Init Order
https://gist.github.com/MangaD/c65a8ea9792c87dab900bbc46ffa3c30
`a(...)` is called first, followed by `b(...)`
then temporary `B{}` is constructed and assigned to b
```cpp
class X {
    A a;
    B b;
public:
    X() : b(...), a(...) { b = B{}; a = A{};}
};
// member initialisation follows declaration order

class X {
    A a;
    B b;
public:
    X() : b(a), a() {}  // looks like b uses a, but a isn't initialized yet!
};
```

```cpp
struct V { V() { /* ... */ } };

struct Base1 { Base1() { /* ... */ } };
struct Base2 { Base2() { /* ... */ } };

struct A { A() { /* ... */ } };
struct B { B() { /* ... */ } };

struct X : virtual V, Base1, Base2 {
    A a;
    B b;

    X() : a(), b() {}
};
```
Construction order for `X`:
1. Virtual base `V` (once, even if shared by multiple bases).
2. Direct base `Base1`.
3. Direct base `Base2`.
4. Non-static data member `a` (declared before `b`).
5. Non-static data member `b`.
6. `X`’s constructor body.
Dtor order is just the reverse


# Multiple inheritance
(Deadly diamond of death)
TODO:

# Object slicing
https://stackoverflow.com/questions/274626/what-is-object-slicing
```cpp
class A {};
class B : public A { int foo;};
void foo(A a);
B b;
A a = b; // all B fields are lost
foo(b); // all B fields like foo are lost

B b1;
B b2;
A& a_ref = b2; // all fields are kept
a_ref = b1; // A's copy assignment called, replacing A's fields
// b2 contains a mixture of b1 and b2

// just delete the move and copy assignments tbh
```

# `std::move`, copy elision, NRVO
`std::move` casts `x` to an `rvalue`. 
`std::move` can ruin NRVO, so don't use in a return

```cpp
std::string s = "hello";
vec.push_back(std::move(s));   // okay if s's old contents no longer matter

class X {
    std::string name;
public:
    X(std::string s) : name(std::move(s)) {}
};
```

Do not use move when there is NRVO
```cpp
std::string f() {
    std::string s = "hello";
    return s;   // preferred
}
auto s = f(); // NRVO constructs directly here rather than moving
```

# Most Vexing Parse
- `Foo x(Bar());` is a function declaration
- `Foo x{Bar()};` is a class initialisation

**Related:**
`vector<string> v{42};` default initialises 42 empty strings because 42 is not a string
`vector<int> v{42};` initialises with 1 element of 42

# Lvalues RValues
https://en.cppreference.com/cpp/language/value_category
```

			 expression
			/           \
	  glvalue           rvalue
	  /     \           /    \
  lvalue   xvalue    xvalue  prvalue

```
- `glvalue`: `lvalue + xvalue`
- `rvalue`: a source of moving
- `prvalue`: temporary/computed value
```cpp
2 + 3;                 // prvalue
std::string{"hello"};  // prvalue if not assigned to an identifier
make_string();         // usually prvalue if returned by value

std::string&& a = std::string{"hello"}; // binds to prvalue
std::string&& b = std::move(s);         // binds to xvalue
```


- `lvalue`: named, still in use
```cpp
int x = 10;
x = 20;    // x is lvalue
```

- `xvalue`: named, but resources may be taken
```cpp
std::string s = "hello";
std::move(s);  // xvalue
```

```cpp
T&       // lvalue reference
const T& // can bind to lvalues and rvalues
T&&      // rvalue reference

// Can accept both lvalue or prvalue
void print(const std::string& s);

std::string s = "hello";
print(s);                    // lvalue
print(std::string{"hello"}); // prvalue

```


# Templates
```cpp
template <typename T> 
void wrapper(T&& x) {
	target(std::forward<T>(x));  // preserve caller category 
}
```


# decltype
Copies a type, preserving qualifiers like `const` or references
```cpp
int x = 10;
const int& y = x; 
decltype(y) b = x; // Type is 'const int&' (preserves the reference)

// if parenthesised evaluates the value category of that expression
decltype((x)) c = x; // Type is 'int&' because (x) is treated as an lvalue expression!

decltype(auto) b = ref; // preserve type, to avoid needing to write auto or auto&

template <typename T, typename U>
auto multiply(T a, U b) -> decltype(a * b) {
	// use trailing return syntax so a and b are defined
	return a * b; 
}
```

# `std::forward<T>`
TODO:

use only in templates with forwarding references to preserve whether the caller passed an lvalue or rvalue
```cpp
template <typename T>
void wrapper(T&& value) {
    target(std::forward<T>(value));
}
```

# SFINAE
Mostly superseded by concepts from C++20
```cpp
// Adapted from Wikipedia
struct Test {
    using Foo = int;
};

// Definition #1
template <typename T>
void f(typename T::Foo) {
    // if T is Test, T::Foo resolves to int
    // allowing Call #1 to succeed
    // SFINAE prevents Call #2 from not causing a compile error
    // though int::Foo doesn't exist
}

// Definition #2
template <typename T>
void f(T) {
    // Call #2 resolves here
}

int main() {
    f<Test>(10); // Call #1. 
    f<int>(10); // Call #2. Without error (even though there is no int::Foo) due to SFINAE.
    return 0;
}
```

# Concepts (C++20)
**Better generics**
```cpp
#include <concepts>
template <typename T> 
requires std::integral<T> || std::floating_point<T>
constexpr double Average(std::vector<T> const &vec) {
    const double sum = std::accumulate(vec.begin(), vec.end(), 0.0);        
    return sum / vec.size();
}

template <derived_from<Player> T>
void processListOfPlayers(const vector<T>& players) {}
// equivalent to Java's <T extends Player>
```

**Concept "interfaces"**
```cpp
template <typename T>
concept Clock = requires(T c) { 
    c.start();  
    c.stop();
    c.getTime();
    {c.name_()} -> std::convertible_to<std::string>;
};
```

# Variadic
```cpp
// typechecking using SFINAE
template<typename... Args,
	typename = std::enable_if_t<(std::is_integral_v<Args> && ...)>>
auto sum(Args... args) {
	return (args + ...); // unary right fold; empty pack is invalid
}

// typechecking using C++20 concepts
template<std::integral... Args>
auto sum(Args... args) { 
    return (0 + ... + args); // binary left fold; empty pack gives 0
}

int result = sum(1, 2, 3, 4);  // 10
```

| Expression         | Name              | Expansion shape     |
| ------------------ | ----------------- | ------------------- |
| `(args + ...)`     | Unary right fold  | `a + (b + c)`       |
| `(... + args)`     | Unary left fold   | `(a + b) + c`       |
| `(args + ... + 0)` | Binary right fold | `a + (b + (c + 0))` |
| `(0 + ... + args)` | Binary left fold  | `((0 + a) + b) + c` |
# Template metaprogramming



