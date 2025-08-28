| Normal Form | Key Idea                             | Removes                          |
| ----------- | ------------------------------------ | -------------------------------- |
| **1NF**     | Atomic values, no repeating groups   | Multi-valued attributes          |
| **2NF**     | Full dependency on whole PK          | Partial dependency               |
| **3NF**     | No transitive dependency             | Redundancy via non-key → non-key |
| **BCNF**    | Every determinant is a candidate key | Edge cases beyond 3NF            |
| **4NF**     | No multi-valued dependency           | Complex redundancy               |
| **5NF**     | No join dependency                   | Over-decomposition anomalies     |