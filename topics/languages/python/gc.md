
See https://daobook.github.io/devguide/garbage_collector.html

1. refcounting
2. for cyclic objects, use cyclic gc (stop-the-world)

3 Generations: 
- if an object survives a cycle, it moves to the next generation



`gc.enable()`, `gc.disable()`, `gc.isenabled()`, `gc.collect()`, `gc.get_count()`, and `gc.get_threshold()`