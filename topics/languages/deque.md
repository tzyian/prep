| Language | Usual deque                  | Internal idea                                | Indexing                     | Nulls              |
| -------- | ---------------------------- | -------------------------------------------- | ---------------------------- | ------------------ |
| C++      | `std::deque<T>`              | Segmented array + a map of fixed-size blocks | O(1)                         | Any `T` permits it |
| Python   | `collections.deque`          | Doubly-linked fixed-size blocks              | Fast at ends, O(n) in middle | Allowed            |
| Java     | `Deque<E>` + `ArrayDeque<E>` | Resizable circular array                     | No `get(i)` API              | Prohibited         |
`std::deque` has several blocks of arrays to avoid moving when pushing back or front

C++ has the indexing map which Python doesn't
C++ derefs 2 pointers for random indexing, Python walks the whole block.
C++ might need to move the map of block pointers
Java will move existing elements like `std::vector`